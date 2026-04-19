# trends-agent-autogen

Live trend data tools for Microsoft AutoGen agents. Connect Trends MCP
and your AutoGen agent gets typed tools for search, social, ecommerce,
news, and developer trends across one MCP server.

> Live trend data for AI agents. One connection. Every platform.
> Powered by [trendsmcp.ai](https://trendsmcp.ai)

## Get your free API key

1. Go to [trendsmcp.ai](https://trendsmcp.ai)
2. Enter your email, key sent instantly
3. 100 free requests per month, no credit card

[Get your free key](https://trendsmcp.ai)

## Install

Use the official AutoGen MCP extension:

```bash
pip install "autogen-ext[mcp]" autogen-agentchat autogen-ext[openai]
```

## Use Trends MCP as AutoGen tools

```python
import asyncio, os
from autogen_ext.tools.mcp import StreamableHttpServerParams, mcp_server_tools
from autogen_agentchat.agents import AssistantAgent
from autogen_ext.models.openai import OpenAIChatCompletionClient

async def main():
    params = StreamableHttpServerParams(
        url="https://api.trendsmcp.ai/mcp",
        headers={"Authorization": f"Bearer {os.environ['TRENDS_MCP_API_KEY']}"},
    )
    tools = await mcp_server_tools(params)

    agent = AssistantAgent(
        name="trend_analyst",
        model_client=OpenAIChatCompletionClient(model="gpt-4o-mini"),
        tools=tools,
        system_message=(
            "You are a senior trend analyst. Use the Trends MCP tools to"
            " answer with real time series data, not opinions."
        ),
    )

    result = await agent.run(
        task="Compare 1 year YouTube vs TikTok trends for 'Stanley cup'."
    )
    print(result.messages[-1].content)

asyncio.run(main())
```

Set the API key in your shell:

```bash
export TRENDS_MCP_API_KEY=YOUR_API_KEY
```

The exact AutoGen MCP API can evolve, see
[microsoft.github.io/autogen](https://microsoft.github.io/autogen) for
the current reference. Both `autogen` (Microsoft) and `ag2` are
supported by similar patterns.

## Use the REST API directly

If you prefer not to use the MCP adapter, register a function tool that
calls the REST API:

```python
import requests

def get_trend(source: str, keyword: str) -> dict:
    """Get a 5 year trend for a keyword on a given source."""
    return requests.post(
        "https://api.trendsmcp.ai/api",
        headers={"Authorization": "Bearer YOUR_API_KEY"},
        json={"source": source, "keyword": keyword},
        timeout=30,
    ).json()
```

## Three tools your AI gets

- `get_trends`, historical time series for any keyword on any source
  (5 years weekly, 30 days daily where supported).
- `get_growth`, percentage change over any period (7D, 1M, 3M, 6M, 1Y,
  YTD, custom dates).
- `get_top_trends`, what is trending right now across live platform feeds.

## All data sources in one connection

One API key gives your AutoGen agents every keyword source and every
live feed below.

### Keyword sources (Get Trends and Get Growth)

| source | What it measures | Keyword format |
| --- | --- | --- |
| `google search` | Google search volume | Any keyword or phrase |
| `google images` | Google image search volume | Any keyword or phrase |
| `google news` | Google News search volume | Any keyword or phrase |
| `google shopping` | Google Shopping search volume | Any keyword or phrase |
| `youtube` | YouTube search volume | Any keyword or phrase |
| `tiktok` | TikTok hashtag volume | Hashtag or topic |
| `reddit` | Subreddit subscribers | Subreddit name only, no `r/` prefix |
| `amazon` | Amazon product search volume | Product name or category |
| `wikipedia` | Wikipedia page views | Article title or topic |
| `news volume` | News article mention volume | Any keyword or phrase |
| `news sentiment` | News sentiment score (positive / negative) | Any keyword or phrase |
| `app downloads` | Mobile app download estimates (Android) | Package id or app identifier |
| `npm` | npm package weekly downloads | Exact package name e.g. react |
| `steam` | Steam concurrent players (monthly) | Game display name e.g. Elden Ring |

All values are normalized to a 0 to 100 scale where the pipeline supports
it, so you can compare different sources directly.

### Live leaderboards (Get Top Trends)

Google Trends, Google News Top News, TikTok Trending Hashtags, YouTube
Trending, X (Twitter) Trending, Reddit Hot Posts, Reddit World News,
Wikipedia Trending, Amazon Best Sellers Top Rated, Amazon Best Sellers by
Category, App Store Top Free, App Store Top Paid, Google Play, Top
Websites, Spotify Top Podcasts, Steam Most Played, GitHub Trending Repos,
IMDb MOVIEmeter, Trending Books.

The source list evolves over time. Authoritative reference at
[trendsmcp.ai/docs](https://trendsmcp.ai/docs).

## AutoGen patterns that work well

- **Single AssistantAgent with tools.** Lowest friction, good for
  research and lookup tasks.
- **GroupChat with a Critic.** Researcher pulls data, Critic checks the
  numbers, Writer drafts the output.
- **Magentic-One style orchestrator.** Use Trends MCP as one of many
  tools available to a planner agent.
- **Multi agent investing or marketing crews.** Combine Trends MCP with
  your existing financial or CRM tools.

## Pricing

Free forever for 100 requests per month. Paid plans available. See
[trendsmcp.ai/pricing](https://trendsmcp.ai/pricing).

## Resources

- [Get a free API key](https://trendsmcp.ai)
- [Documentation and full reference](https://trendsmcp.ai/docs)
- [Pricing](https://trendsmcp.ai/pricing)
- [All TrendsMCP repositories](https://github.com/trendsmcp)

## Security

Never commit your API key to a public repository. Use environment
variables or your OS secret store. Keys can be rotated at any time from
your TrendsMCP dashboard. The `YOUR_API_KEY` placeholder above is a
literal string to replace with your own key after you copy the snippet
locally.

## License

MIT. See [LICENSE](./LICENSE).
