---
name: excel-formulas
description: "Excel formulas. Use it when the person asks for: A spreadsheet formula as a column."
---

This skill is Kater's guide `vfs://guides/excel-formulas.md`.

# Excel formulas

An Excel formula is one column of a query's result, written in spreadsheet syntax. It lives in a workspace query or a Sandbox statement, or, once promoted, on its workspace root, never in shared modeling. The compiler turns it into the connection's warehouse SQL each time the query compiles, so the file keeps only the formula's text.

## How one is written

A field's whole value is `EXCEL('=…')`, inside a `CUSTOM_CALCULATION` declaration:

```sql
SELECT
  shop.region,
  EVAL(shop.sum__amount),
  EVAL(shop.count_distinct__order_id),
  CUSTOM_CALCULATION('order_value', EXCEL('=ROUND([@sum__amount]/[@count_distinct__order_id], 2)')) AS "Order Value",
  CUSTOM_CALCULATION('change', EXCEL('=[@sum__amount]-[@sum__amount:-1]')) AS "Change"
FROM workspace.shop AS shop
GROUP BY shop.region
ORDER BY 2 DESC
```

- `EXCEL(…)` is all of the field's value. It is never part of a larger expression.
- Every formula column in a query is a `CUSTOM_CALCULATION`, whatever it reads. A column written before as `DIMENSION` or `MEASURE` has a warning: apply its fix, Declare with CUSTOM_CALCULATION. A write that asks for a formula column as a measure or a dimension is refused; promote it instead.
- What it can be promoted as follows from what it reads: a formula that reads measures, or a measure's total, on its own row can become a `MEASURE`; one that reads only row values on its own row can become a `DIMENSION`; one that reads other rows, a range or a whole column stays in its query.
- A quote inside the formula's text is written twice, as in any SQL string. Text inside the formula goes in double quotes.
- The syntax is US Excel's: a comma between arguments and a point in a number.

## References

A formula reads the columns of its own query's result, and nothing else. A field the query doesn't show has to be added to the query first.

One formula fills its column. Typed on any row, it is what every row of the column reads, as an Excel fill-down is, and no cell keeps a formula of its own. References count from the row they are typed on: `=C9+D11` typed on row 10 is stored as `[@spend:-1]+[@revenue:+1]`, which `--row 10` says on the command line, and the formula bar reads it as `=C4+D6` on row 5. A formula typed on a later row that reads rows above it, such as `=AVERAGE(C2:C4)` on row 4, gets the note `excel_formula_from_later_row`: every row has the formula, so the first rows read fewer rows above them, where an Excel fill-down from row 4 leaves the rows above it empty.

| In a file | In the formula bar, on row 5 | Reads |
| --- | --- | --- |
| `[@revenue]` | `B5` | this row's value |
| `[@revenue:-1]` | `B4` | the row above; `:+1` is the row below |
| `[@revenue:$1]` | `B$1` | the first row's value |
| `[@revenue:$3]` | `B$3` | the third row's value |
| `[@revenue:$1]:[@revenue]` | `B$1:B5` | the first row down to this one |
| `[@revenue:-2]:[@revenue]` | `B3:B5` | this row and the two above |
| `[@revenue:$2]:[@revenue:$13]` | `B$2:B$13` | the rows 2 to 13, the same on every row |
| `[revenue]` | `B:B` | every row |
| `[#total revenue]` | `B_TOTAL` | the measure worked out over every row at once |

A file names a column by its field name, so a column that moves breaks nothing. The formula bar shows letters, and turns them into names when the formula is written.

- A column is named by the name its field is declared with, its alias, or, with no alias, the name of the one source field it shows: `EVAL(shop.sum__amount)` is `[@sum__amount]` and `KATER__MONTH(shop.order_month)` is `[@order_month]`. An unaliased expression over two fields or more has no name a formula can use: give it an alias.
- "The row above" follows the query's `ORDER BY`. A query with no `ORDER BY` has the compiler's default order, so no formula needs a sort of its own. The values change when the sort does. A formula in a SELECT inside the query gets no default order, so that SELECT needs an `ORDER BY`. For "the previous month, however the table is sorted", use the Lag and period operations instead.
- A row's number in the sheet is its place in the query's order. Rows that Find's "Only matching rows" hides and a sort on screen (its mark says "Sorted on screen") never change it, so a reference and the address's `row` name the same row.
- `SUM([revenue])` adds up the values shown. `[#total revenue]` is the measure at the total's grain, which is the right denominator for an average, a ratio or a distinct count.
- Row 1 has no row above and the last row has no row below, so a value that reads past them is empty. The formula bar shows such a reference as `#REF!`, as Excel does: `[@a]-[@a:-1]` reads `=B1-#REF!` on row 1. A file never holds `#REF!`, and a formula that holds one is refused.
- A sort by a formula that reads another row is refused, by its name and by its place: "The query is sorted by this column, and its formula reads another row in that order. Sort by another column." A formula that names its own column is refused too.
- A formula reads only the rows the query returns, after its `LIMIT`: the last row has no row below, and a range or a whole column ends at the last row. A sort by a formula chooses the rows by its value over every row, then works it out over the rows kept. A formula that also reads a total reads every row.
- A column that is itself worked out over rows can be read on its own row, and not on another row or in a range.
- A row's number, as in `B$3`, is its place in the query's order, from 1, among the rows the query returns. A range from a fixed row other than row 1, such as `B$2:B$13` or `B$2:B5`, reads the rows by their numbers, as a criterion worked out from the row does: a query cannot sort by such a formula, and it cannot also read a total.
- A formula reads only the columns of its own query, never another sheet, such as `'Region Mix'!C5`. Bring the value into the query as a field.

## Functions

- Logic: `IF`, `IFS`, `SWITCH`, `AND`, `OR`, `NOT`, `IFERROR`, `IFNA`, `ISBLANK`.
- Math: `ABS`, `ROUND`, `ROUNDUP`, `ROUNDDOWN`, `TRUNC`, `INT`, `CEILING`, `FLOOR`, `MOD`, `POWER`, `SQRT`, `EXP`, `LN`, `LOG`, `LOG10`, `SIGN`.
- Text: `LEFT`, `RIGHT`, `MID`, `LEN`, `LOWER`, `UPPER`, `PROPER`, `TRIM`, `CONCAT`, `CONCATENATE`, `SUBSTITUTE`, `REPLACE`, `FIND`, `SEARCH`, `EXACT`, `REPT`, `VALUE`, `TEXT`.
- Date: `DATE`, `YEAR`, `MONTH`, `DAY`, `HOUR`, `MINUTE`, `SECOND`, `TODAY`, `NOW`, `EDATE`, `EOMONTH`, `DAYS`, `DATEDIF`, `WEEKDAY`, `WEEKNUM`, `NETWORKDAYS`.
- Over rows: `SUM`, `AVERAGE`, `MIN`, `MAX`, `COUNT`, `COUNTA`, `MEDIAN`, `STDEV`, `STDEV.S`, `STDEV.P`, `VAR`, `VAR.S`, `VAR.P`, `RANK`, `RANK.EQ`, `SUMIF`, `SUMIFS`, `COUNTIF`, `COUNTIFS`, `AVERAGEIF`, `AVERAGEIFS`, `MINIFS`, `MAXIFS`, `MATCH`, `INDEX`, `XLOOKUP`.

A criterion of an `-IF` function is a fixed value, a comparison such as `">100"`, text with `*` and `?`, or this row's own value of the same column, which takes the rows that share it: `SUMIF([region], [@region], [revenue])` is the sum over this row's region.

A criterion can also be worked out from this row: another column, an expression or a comparison joined to a value. `SUMIFS([revenue], [channel], [@channel], [week], [@week]-7)` is the same channel's week before, and `SUMIFS([revenue], [week], "<="&[@week])` is the sum up to this row's week. A row with no match gives 0, and is empty for `AVERAGEIF` and `AVERAGEIFS`. `*` and `?` work only in fixed text, and such a criterion cannot read a total or a column that is itself a read of this kind. A query cannot sort by such a formula, and a query that has a measure of its own cannot show it to a query that reads it.

A measure that cannot be added up across a dimension, such as spend across currency (`NOT_ADDITIVE_ACROSS`), has no total and no running total: `[#total spend]`, `SUM([spend])` and `SUM([@spend:$1]:[@spend])` are refused. `SUMIF([currency], [@currency], [spend])` adds it up for each currency.

`SUBTOTAL(9, [revenue])` is the function its number names, here `SUM`, since a query's rows are the rows it shows. `OFFSET([@revenue], -24, 0)` is `[@revenue:-24]`: only written numbers, a column offset of 0 and no other size. A criterion such as `">="&DATE(2026,1,1)` compares with the date.

A lookup finds a row of the query's own result. `MATCH(B5, A:A, 0)` is the number of the first row, in the query's order, whose cell of A equals B5, `INDEX(C:C, MATCH(B5, A:A, 0))` is that row's cell of C, and `XLOOKUP(B5, A:A, C:C, "none")` is the same cell, or "none" where no row matches. `=INDEX(A:A, MATCH(MAX(C:C), C:C, 0))` is the month with the most revenue. A value that more than one row holds gives the first of them, and no match gives an empty value, as does an empty value to look up. Each range is a whole column of the query, and `MATCH` takes only 0, its exact match. Text is compared matching case. A lookup reads the rows as a criterion worked out from the row does, so a query cannot sort by it.

A lookup elsewhere, into another query, a model or cells typed on the sheet, is a join: add the field that holds the value to the query, and read its column. So `VLOOKUP`, `HLOOKUP`, `LOOKUP` and `XMATCH` are not supported.

Every other Excel function is not supported, such as `INDIRECT`, `LET`, `LAMBDA`, functions that fill more than one cell, and regression statistics.

## Where values differ from Excel

The syntax is Excel's and the values are the warehouse's.

- A value Excel would show as an error is empty: a division by zero, 0 to the power of 0 or less, the root of a negative number, `VALUE` of text that isn't a number, a start below 1 or a count below 0 in `LEFT`, `RIGHT`, `MID`, `REPLACE` or `REPT`. No cell holds `#DIV/0!`. A start or a count with a fraction takes its whole part, as in Excel.
- An empty cell of numbers is 0 in arithmetic, as in Excel: `[@a]+1` is 1 when `a` is empty. A value that the formula works out and that is empty, such as a division by zero, stays empty, and so does `[@a:-1]` on the first row. In a comparison an empty value stays empty: `[@a]>0` is empty when `a` is, where Excel reads a blank cell as 0. In `AND`, a false condition still gives FALSE. `IFERROR` gives its fallback for every empty value, and `IF` with no third argument is empty when its condition is false.
- Text where a number, a date or a condition goes, such as `[@a]*"x"` or `ROUND([@name],1)`, is refused when the formula is checked, with words that name the function, the argument and the column. Text written in the formula that reads as that kind runs: `[@a]*"2"`. A date where a number goes reads as its serial number, and TRUE or FALSE as 1 or 0. `+` or `-` with a date stays a date.
- `""` beside another value in `IF`, `IFS`, `SWITCH`, `IFERROR` or `IFNA` is an empty value, where Excel gives empty text: `=IFERROR([@a]/[@b]-1,"")` is a number or empty. It stays empty text only beside text that the formula writes, such as `"big"`.
- A TRUE or FALSE is above every number in a comparison, as in Excel: `([@a]>5)=1` is FALSE and `([@a]>5)>100` is TRUE.
- Two numbers compare as Excel shows them, to 15 significant digits: `1/10*3=0.3` is TRUE.
- `=` and `<>` match text without case, as in Excel, and so do the criteria of `SUMIF`, `COUNTIF` and the other `-IF` functions: `"apple"` finds `Apple`. `FIND` and `EXACT` match case. `<` and `>` order text with case.
- `SUM`, `MIN`, `MAX`, `SUMIF`, `SUMIFS`, `MINIFS` and `MAXIFS` over no numbers are 0, as in Excel, and `AVERAGEIF` and `AVERAGEIFS` are empty. Text in their range counts as no number. `MIN`, `MAX`, `MINIFS` and `MAXIFS` over a column of dates are empty when no date is found.
- A criterion of `""` matches empty cells, `"<>"` matches the cells with a value, and `"<>5"` also matches empty cells. A criterion that refers to an empty cell, such as `[@b]`, is 0, as in Excel: in a column of numbers it matches each 0 and no empty cell, and in a column of another kind it matches nothing. A cell of another kind than the criterion's value does not match, and `<>` takes it. `COUNT` counts numbers and dates, not text or TRUE or FALSE, so `COUNT` over a text column is 0. `COUNTA` counts every cell that has a value.
- A date joined to text reads as `yyyy-mm-dd`, where Excel writes its serial number. The check warns with `excel_date_joined_to_text`: write the date with `TEXT`, such as `TEXT([@order_date],"yyyy-mm-dd")`, to say how it reads.
- `MOD` takes the number's sign. `TRIM` keeps the spaces between words.
- `TEXT` writes a number as `"0"`, `"0.00"`, `"#,##0.00"` or `"0.0%"`, and a date from `y`, `m`, `d`, `h` and `s`, with `ddd` and `dddd` for the day's name: `"yyyy-mm-dd"`, `"m/d/yyyy"`, `"dddd, mmm d"`. It writes no currency symbol.

## In the worksheet

The rows of a query, in the query editor and in the Sandbox, are a worksheet: lettered columns, numbered rows, and a formula bar above them. A person writes a formula in the cell, and the bar mirrors it.

- A formula column always holds a formula. A new one opens ready to write: its cell in row 1, or the row where a person typed, holds `=` and the caret, and the bar shows the same draft. Escape with nothing typed removes the column. After typing, Escape goes back to its empty cell, and a second Escape removes the column. A field column is never changed there.
- Every cell takes a click. Blank rows run under the rows and empty lettered columns run after the last column, and neither is in a result, a copy or an export. `=` or a character on an empty column's cell starts a formula column on that row. A key on a blank row under the rows only says that the row has no values, since the rows cannot grow.
- The formula bar is one line. Its chevron or Ctrl+Shift+U expands it to four lines, and the address keeps that as `bar=open`. On a calculation, its ✕ is Escape and its ✓ writes as Enter does and keeps the same cell selected. The `=` in an Excel formula column's heading opens the formula in its cell. A double-click on a cell grows it to show all it holds.
- A column's heading and the formula bar show its mark: ƒ for a measure, the pyramid for a calculation written in SQL, the pyramid with fx for an Excel formula, and the data type's mark for a dimension.
- A click on a cell writes its reference only where the caret is at a place for one: after `=`, `(`, `,`, `:`, an operator, or a reference just selected, which the new one replaces. Elsewhere a click writes the formula and selects the cell. The label at the bar's right reads Ready, Enter, Edit or Point.
- Enter writes the formula and moves down, Tab moves right, Escape drops it. A formula with an error is not written. Enter closes the parentheses a formula leaves open, as Excel does: `=ROUND(D1/E1, 2` is written as `=ROUND(D1/E1, 2)`.
- A function picked from the list is written with its open parenthesis, such as `ROUND(`, and one that takes nothing is written whole, such as `NOW()`.
- A toolbar over the bar, and Format in a column's menu, format a column, sort, filter, total and freeze. Each click is one edit of the file: `format.display` writes `-- @format`, `format.style` writes `-- @style`, `format.highlight.add` writes `-- @highlight`, and `set_freeze` writes `freeze_rows` and `freeze_columns`. Each is the column's, for every row. See [Dimension](vfs://guides/dimension.md) and [Overview](vfs://guides/overview.md). Write the same lines the toolbar writes.
- A column's menu has Rename, Edit formula, Insert formula column left and right, Duplicate, Sort, Format, Convert to SQL, Promote as Measure or Promote as Dimension (where the formula can be one), and Delete field. Convert to Excel formula shows on a column written in SQL that Kater can read as a formula. A converted column keeps its name, its label and its declaration.
- A formula that reads another row reads it in the query's order. A query with no `ORDER BY` has the compiler's default order (R-ORDER-1): its first date, newest first, else its first measure, largest first, else its first dimension, A to Z, with the other grouping fields breaking ties. The file keeps no `ORDER BY`. The rows come back in the order the formula read them in, ties included.
- Enter runs the formula for one row before it writes it. A formula the warehouse can't run is refused with the warehouse's reason, and nothing is written.
- A chart can show every column of its query, and a formula that reads other rows is one of them. The chart draws the query's rows as the table shows them, and never works a measure out again.
- The page's address holds the selected cell as `column=<field name>&row=<number>`, from 1. A row past the last row names a blank row. An empty column's cell and a draft are never in the address.

## When to write one

- Write plain Kater SQL by default. Write an Excel formula when the person asks for one, or when changing a column that already is one: keep the form the person chose.
- Read an `EXCEL('=…')` field as you read any field. `kater new field QUERY --excel "=B5-C5" --row 5` previews one as the formula bar holds it, and `--write` adds it.
- A formula that reads only its own row can move to the workspace root: `kater model promote NAME --relation QUERY --path QUERY.sql --promote-as measure` (or `dimension`) writes `MEASURE('name', EXCEL('=…'))` or `DIMENSION('name', EXCEL('=…'))` there, and the query reads that field. Without `--promote-as`, the dry run answers `promote_as_needed`, and its `promote_as` names the kind the formula can be. One that reads a range or another row depends on the result's shape and stays in its query (`not_promotable`).
- To ask the data team for it in a shared model, send the same kind: `kater promotion request QUERY.sql NAME --promote-as measure --reason "…"`, through `kater_apply` once the person says yes. Without the kind, the request is refused and says which kind the formula can be. The data team gets `MEASURE('name', EXCEL('=…'))` or `DIMENSION('name', EXCEL('=…'))` in the shared model, and the query reads that field.
