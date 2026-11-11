# Kater

Answer questions about your company's business data from its governed warehouse, and build charts, dashboards and reports on it.

This repository is the plugin marketplace `kater`, which holds the plugin `kater` at the version 0.2.3294+0cea1823cd65. Each Kater release updates it; nobody edits it by hand. The plugin holds Kater's instructions, its skills and the Kater MCP, which you sign in to with your Kater account.

| Host | Install | Update |
| --- | --- | --- |
| Claude Desktop and claude.ai | In **Customize › Plugins**, select **Add › Add marketplace › Add from a repository**. Enter `kater-ai/kater-agent`, select **Sync**, then install **Kater**. On the Kater plugin's **Connectors** tab, connect Kater and sign in with your Kater account. In a Team or Enterprise organisation, an Owner installs it for everyone in **Organization settings › Plugins & skills › Add › Sync from GitHub**. Enter `kater-ai/kater-agent` and sync. Then, on Kater's row, set **Default access** to **Installed by default**. An Owner also adds Kater in **Organization settings › Connectors**, and members then only connect. | Select **Check for updates** on the marketplace in **Customize › Plugins**, or keep **Sync automatically** on. |
| Claude Code | `claude plugin marketplace add kater-ai/kater-agent`, then `claude plugin install kater@kater`. Start Claude Code in any folder, then run /mcp, select plugin:kater:kater and sign in with your Kater account. | `claude plugin marketplace update kater`, then `claude plugin update kater@kater`. |
| Codex | `codex plugin marketplace add kater-ai/kater-agent`, then `codex plugin add kater@kater`. In a terminal, run `codex mcp login kater` and sign in with your Kater account, then start Codex in any folder. Trust Kater's hook when Codex asks: it gives the agent Kater's instructions. | `codex plugin marketplace upgrade kater`, then `codex plugin add kater@kater`. |

What the plugin sends to a model and what is kept: [AI data security](https://landing.kater.ai/docs/admin/ai-data-security/).
