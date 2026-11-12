---
name: modeling-practices
description: "Modeling practices. Use it when the person asks for: Data definitions that everyone shares: measures, dimensions and joins."
---

This skill is Kater's guide `vfs://guides/modeling-practices.md`.

# Modeling practices

How to model data so that it is grouped, formatted and compared the way a person expects. Read it before you add or change a field in a Kater dataset or a semantic model, and when a field shows a grain, a type or a value that a person did not expect. Each rule below comes from a real case, which the Cases section tells: what we saw, the problem it caused, and the practice that prevents it. A case is added each time a model surprises someone, with its rule in the list.

## Rules

### Dates and times

- Keep a date at the type its values have. A `DATE` column stays a `DATE`. Do not cast it to a timestamp and back, as `TO_DATE(TO_TIMESTAMP(date_day))` does: it changes nothing and makes a reader think the source held text. (Case 1)
- Never store a date as formatted text, such as `Jul 2025` or `2025-07`. Keep the date, group it with a grain (`KATER__MONTH`), and show it with a format. Text sorts as text, cannot be compared by period, and cannot change grain. (Case 1)
- When a source does hold a date as text, parse it one time, in the Kater dataset, with its exact format: `TRY_TO_DATE(month_label, 'MON YYYY')`. Then group the parsed date. Never cast text to a timestamp and hope the warehouse reads it. (Case 1)
- Keep the finest grain the source has. A reader can group a day by month, but it cannot get days back from a month.
- A grain is part of the data, and it follows the field. A column that a query groups by month is a month in every query that reads it, through a plain column, a cast, `MIN` and `MAX`. So a reader of a monthly query shows Month with no `KATER__MONTH` of its own. To see days or weeks, read the source field, not the monthly query. (Case 1)
- Union branches at one grain. A `UNION ALL` of a monthly branch and a daily branch holds days, so its column shows as a raw date, not Month. Group every branch to the same grain, or group the union after it: `KATER__MONTH(u.date_day)` over the union.
- Declare a column that already holds periods with its grain, `DIMENSION('date_month', KATER__MONTH(src.date_day))`, and name and label it for the period ("Reporting Month"). Its grain is then known, and a person does not group it finer by mistake. (Case 1)

### Codes and names

- Map a code to the name people use one time, in the shared model, as its own field beside the code. A query that writes its own `CASE` for the names spells them differently from the model, and two charts then show two names for one thing. (Case 2)
- Let the code's description and the name's description each say which one to use for what. (Case 2)

### Amounts and units

- Keep an amount's unit, such as its currency, as a field beside it, and format the amount by that field: `-- @format currency(union__currency, 2)`. (Case 3)
- An amount adds up only within one unit. Declare that on the measure with `NOT_ADDITIVE_ACROSS(unit)`, and group or filter by the unit in each query that totals it. (Case 3)

## Cases

### Case 1: Reporting Date looks like text and shows Month

**What we saw.** In the Paid Media Review workspace, the query `monthly_paid_spend` showed Reporting Date as `Jul 2025`, `Aug 2025`, and so on, and the field picker ticked a grain under Reporting Date. The shared model `ad_reporting` declares the field as `DIMENSION('union__date_day', base.date_day)`, over `TO_DATE(TO_TIMESTAMP(date_day))` in its dbt CTE. It looked as if the source held month names as text, the model cast them to a timestamp, and the result was called Month with no `DATE_TRUNC` or `KATER__` function.

**The problem.** The source column `DATE_DAY` is a `DATE`, one row for each day. The cast to a timestamp and back to a date changes no value, but it hides that, and it reads like a parse of text. The month came from the query itself: `monthly_paid_spend` selects `KATER__MONTH(ad_reporting.union__date_day)`, which keeps the field's name `union__date_day` and its label, and a month shows as `Jul 2025`. A query that reads `monthly_paid_spend`, such as `monthly_return_leaders` over `platform_monthly_return`, selects the plain column and still shows Month, because the values are first days of months: the grain follows the data, not the function in the reader's own text.

**The practice.**
- In the Kater dataset or the semantic model, keep `date_day` as the `DATE` it is: `base.date_day`, with no round trip through a timestamp.
- Group in the query, with `KATER__MONTH`, and let the month format show it. Never make a text column such as `Jul 2025` in the model.
- When a field holds periods, name and label it for the period (`date_month`, "Reporting Month"), as `ad_reporting__monthly_campaign_region_report` does, so nobody reads a month as a raw day.
- To check a field's grain, read its source column's type in the database dataset (`modeling/db_objects/`), then the first function around it in the query that shows it.

**Since then.** Kater's own Ad Reporting models read `date_day` as the `DATE` it is, with no round trip through a timestamp.

### Case 2: A query spelled the platform names its own way

**What we saw.** The shared model `ad_reporting` has Platform, the codes `facebook_ads`, `google_ads` and `tiktok_ads`, and Ad Platform, the names Google Ads, Meta Ads and TikTok Ads. The query `paid_spend_by_platform` in the Lifecycle Brief Paid Media workspace grouped by Platform and wrote its own `CASE` to turn the codes into names.

**The problem.** The query's names are `Meta ads` and `TikTok ads`, and the model's are `Meta Ads` and `TikTok Ads`. A chart of the query and a chart that reads Ad Platform show two names for one platform, and a filter on one name does not match the other.

**The practice.**
- Map codes to names one time, in the shared model, and group by that field: `ad_reporting.platform_name` (Ad Platform, a `KATER__MAP` of the codes), not a `CASE` of your own.
- Each field's description says which one to use, as Platform's does: "To show or group by the platform's name, use Ad Platform."

### Case 3: Spend in many currencies adds up to one figure

**What we saw.** The shared model `ad_reporting` holds each account's spend in the account's own currency, with Currency beside it.

**The problem.** A total of Spend over all accounts adds US dollars to other currencies, which gives a figure in no currency.

**The practice.**
- Keep Currency as a field, and format Spend by it: `-- @format currency(union__currency, 2)`.
- Declare the rule on the measure: `MEASURE('sum__spend', SUM(base.spend), NOT_ADDITIVE_ACROSS(union__currency)) AS "Spend"`. A query that adds Spend across Currency is then refused with `not_additive_across`, with a fix that groups by it.
- Each query that totals spend groups by Currency or filters to one, as `monthly_paid_spend` groups by it.
