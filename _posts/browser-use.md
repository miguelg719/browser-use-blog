---
title: "Browser-Use: Have Llama do the work"
excerpt: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Praesent elementum facilisis leo vel fringilla est ullamcorper eget. At imperdiet dui accumsan sit amet nulla facilities morbi tempus."
coverImage: {
  src: "/assets/videos/demo_browser_use.mp4",
  isVideo: true,
  poster: "/assets/images/thumbnail.png"
}
date: "2024-12-02T05:35:07.322Z"
author:
  name: Miguel Gonzalez
ogImage:
  url: "/assets/blog/dynamic-routing/cover.jpg"
---

In the ever-expanding universe of human-computer interaction, a new frontier is emerging—one where artificial intelligence doesn't just assist, but fundamentally transforms how we navigate digital landscapes. Browser (or computer) use agents represent more than a technological innovation; it's a paradigm shift in how we conceptualize web interactions.

Consider the typical web task—filling out a multi-step form, conducting research across different websites, or managing complex digital workflows. Web users have mastered the art of completing these tasks through repetition and experience, and websites have evolved to simplify the process. Even as streamlined as this process currently is, research shows that the average user spends more than 5 hours daily navigating web interfaces. Repetitive tasks like form filling, data collection, and cross-site research consume up to 60% of productive work time. Imagine reclaiming 90% of that time, returning more than 2 hours a day of productive work to users. The implications are profound:

- Dramatic increases in individual and organizational productivity
- More time for creative problem-solving and collaboration
- Enhanced focus on high-value strategic work
<!-- - Reduced cognitive fatigue -->

Today, with the rise of AI models like [**Llama 3.2**](https://www.llama.com/), the dream is becoming a reality. The power of LLMs has reshaped how computers understand intent, and Llama models excel at understanding you, your questions, your needs. Other providers are already offering similar capabilities (most of the times behind closed curtains and paywalls) but Meta's commitment to open source provides a long-term advantage. No matter where the current ceiling is, open source development is always catching up to the latest advancements by private companies thanks to efforts like Llama. By leveraging open source development, a small team can build products that compete with large companies within a fraction of the cost and without compromising performance. This is the power of open source.


### **Browser-Use Llama**

In this post, we introduce a new browser agent that leverages Llama 3.2 to help you complete tasks on the web. The agent is enhanced with a set of tools that allow it to interact with the web in a way that is more natural and human-like: by clicking, scrolling, and typing. We use [**Playwright**](https://playwright.dev/python/) (an improved version of Puppeteer) as the interface to the browser, release a set of prompts and a framework for providing context that enhances the agent's performance/accuracy, and provide a quickstart to encourage the open source community to build upon it.


Tool use refers to the ability of an agent to leverage external utilities or systems (most of the times through APIs) to accomplish tasks it couldn’t complete on its own. For example, a model that can't do math can use a calculator API to get the result of an equation. Playwright emerged as an advanced browser automation testing framework (that improved on the capablitities of it predecessor Puppeteer). Today, we are repurposing it to become the LLM's gateway to the web. By carefully crafting the tools for the agent through Playwright scripts, we can provide the agent with the ability to navigate, click, and type on web pages. For example:

```python
"""
Consider output as the generated response from Llama. 
  output = {"action": "navigation", "url": "https://www.google.com"}
    or 
  output = {"action": "click", "selector": "button=Search"}
And page as a browser webpage. 
"""

if output["action"] == "navigation":
  await page.goto(output["url"])

elif output["action"] == "click":
  selector = output["selector"].split("=")
  selector_type = selector[0]
  selector_name = selector[1]
  await page.get_by_role(selector_type, name=selector_name).first.click()

elif output["action"] == "fill":
  selector = output["selector"].split("=")
  selector_type = selector[0]
  selector_name = selector[1]
  await page.get_by_role(selector_type, name=selector_name).fill(output["value"])

```

How can the agent know which action to take at any given time? The answer is context. Context refers to the information or background that helps a model understand and respond accurately to a task or query. In browser-use, providing the agent with digestable context is the single most important factor to improve its accuracy at completing tasks. We provide context by storing a history of messages between user and agent, the history of actions performed by the agent, a screenshot and the accessibility tree of the current page. Historically, web automation relied heavily on HTML parsing and CSS selectors to extract information. While effective in static environments, this approach struggles in today’s web ecosystem, where optimization builds show little visibility into how they render content. For this reason, we pass the accessibility tree to the agent as the interface to the current page. [**Accessibility trees**](https://www.boia.org/blog/what-is-a-websites-accessibility-tree) provide a simplified representation of the elements in the page, so the agent can 'see' and interact with the page. Notice how see is in quotes; it doesn't provide the best analogy. In fact, how the agent actually sees the page is through a screenshot. For this reason, the new Llama 3.2 vision models are excellent candidates for our application, because they are one of the first open source multimodal LLMs that can understand both text and images alike. 

We are releasing a quickstart under llama-recipes to encourage the open source community to build upon it. Our work exists thanks to the work of giants, and this is our way of doing our part to give back. To dive deeper into browser use check out the quickstart notebook in 

[**https://github.com/meta-llama/llama-recipes/tree/main/recipes/use_cases**](https://github.com/meta-llama/llama-recipes/tree/main/recipes/use_cases)


### **Next steps**

Some ideas on how to improve the agent further:

- Using web-task datasets such as [**Mind2Web**](https://osu-nlp-group.github.io/Mind2Web/) to fine-tune Llama on agentic web workflows. 
- Implementing a scalable framework for Llama-powered browser agents leveraging [**llama-stack**](https://github.com/meta-llama/llama-stack).
- Grounding the screenshot and accessibility tree with OCR + element linking. 


Keep hacking. 