# Kater

Answer questions about your company's business data from its governed warehouse, and build charts, dashboards and reports on it.

This repository is the plugin marketplace `kater`, which holds the plugin `kater` at the version 0.2.2882+96add0d788b2. Each Kater release updates it; nobody edits it by hand. The plugin holds Kater's instructions, its skills and the Kater MCP, which you sign in to with your Kater account.

| Host | Install | Update |
| --- | --- | --- |
| Claude Desktop and claude.ai | In **Customize › Plugins**, select **Add › Add marketplace**, enter `kater-ai/kater-agent` and install **Kater**. Then open its **Connectors** tab and connect Kater with your Kater account. In a Team or Enterprise organisation, an Owner adds the marketplace for everyone in **Organization settings › Plugins & skills › Add › Sync from GitHub** and sets Kater to **Installed by default**; members then only connect. | Select **Check for updates** on the marketplace in **Customize › Plugins**, or turn on **Sync automatically**. |
| Claude Code | `claude plugin marketplace add kater-ai/kater-agent`, then `claude plugin install kater@kater`, then `/mcp` in a session to sign in to Kater. | `claude plugin marketplace update kater`, then `claude plugin update kater@kater`. |
| Codex | `codex plugin marketplace add kater-ai/kater-agent`, then `codex plugin add kater@kater`, then `codex mcp login kater`. At the next start, trust the plugin's hook when Codex asks: it gives the agent Kater's instructions. | `codex plugin marketplace upgrade kater`. |

What the plugin sends to a model and what is kept: [AI data security](https://landing.kater.ai/docs/admin/ai-data-security/).
