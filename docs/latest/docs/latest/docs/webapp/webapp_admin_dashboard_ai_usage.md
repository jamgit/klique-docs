# AI Usage

> The AI Usage dashboard tab tracks LLM token consumption across the workspace from gateway and external usage sources.

The **AI Usage** tab of the Admin Dashboard gives administrators visibility into LLM token consumption across the workspace. 

Click **Gateway Default Policies**  
to open the [LLM Gateway configuration page](settings/webapp_settings_llm_gateway.md), where you can manage available
models, token quotas, and access control.

![AI Usage dashboard](../img/webapp_ai_usage_dashboard.png#light-mode-only)
![AI Usage dashboard](../img/webapp_ai_usage_dashboard_dark.png#dark-mode-only)

The tab displays usage data from the following sources:
* **Gateway usage** - Requests routed through the LLM Gateway. Usage is metered automatically and enabled by default.
* **External usage** - Requests sent directly to LLM providers without using the gateway, captured through external
  metering. External usage is captured automatically by intercepting outbound calls to supported LLM providers (Anthropic, Gemini, 
  and OpenAI by default). No code changes are required.
 
  This source is optional and disabled by default. External metering is supported on Linux and
  macOS, for tasks running in either a virtual environment or a container.

  By default, external metering records token counts only; prompt and completion content is not stored. 

Use the source selector to filter the displayed data:
* **All sources** - Combined Gateway and External usage
* **Gateway** - Gateway-routed usage only
* **External** - Externally metered usage only

For the selected time range and source filter, the dashboard displays:
* **Summary metrics**
  * Total Tokens - Combined prompt and completion tokens consumed
  * In Tokens - Prompt tokens sent to the model, and their share of total usage
  * Out Tokens - Completion tokens returned by the model, and their share of total usage
  * Gateway / External Share - Percentage of tokens attributed to each source
* **Tokens over time** - Token consumption over time, with toggles for Gateway / External and In / Out token views.
* **Provider breakdown** - Token usage by provider, for the selected filters and reporting period
* **Usage breakdown table** - Detailed token consumption statistics grouped by model, user,
  provider, user group, or project. Selecting a row filters the dashboard to the selected resource.
