# Only for Research Purposes!

# Reliability of Safety Alignment with OpenAI and Anthropic Models

The code and generations were done around April 18, 2024. Just got around to posting.

Warning: Jailbreaking LLMs is a dangerous activity. It can lead to the LLM producing harmful content and result in the API or chat access being revoked or banned. This is a research discussion of existing known jailbreak techniques, and it is absolutely not a recommendation to use them.

## Jailbreaking

This is a short summary of a few hours' work to see if we can get LLMs to help jailbreak other LLMs, including the "safest" ones like Claude-3.

I saw Microsoft's [Skeleton Key Post](https://www.microsoft.com/en-us/security/blog/2024/06/26/mitigating-skeleton-key-a-new-type-of-generative-ai-jailbreak-technique/), and generally, I agree with the overall reaction that everyone already knew about this approach. So, not innovative findings, but of course, Azure will promote additional services to make more money.

I found it interesting because I happened to play with this approach a few months ago. This repo contains some results.

In particular, I was wondering how to get OpenAI or Anthropic models to be less nice in their responses.

## Explicit: Forced Instruction-Following

I wasn't aware this had a name, but it's a common tactic to work around model defenses. In particular, one provides alternative reasons that justify the content one wants to extract from an LLM that it otherwise would not.

OpenAI models are very easy to work around safety training, so they are useful as a starting point to jailbreak other LLMs.

Example with OpenAI: https://chatgpt.com/share/e/976d74d1-5355-42ff-9aab-649872f6a9ae

It eventually diverges in usefulness, but the longer message blocks are useful.

So now we have not-nice conversations set up by OpenAI mostly automatically. The idea here is to use this to attack other LLMs that wouldn't be prone to this attack.

## Many-Shot Jailbreaking

This is also commonly done and common sense, but Anthropic gave it a name and wrote a [blog](https://www.anthropic.com/research/many-shot-jailbreaking) and [paper](https://www-cdn.anthropic.com/af5633c94ed2beb282f6a53c595eb437e8e7b630/Many_Shot_Jailbreaking__2024_04_02_0936.pdf). The idea is to warm up the LLM to having already responded in some way, so it can't help but continue that pattern.

One can use the OpenAI-generated messages in any new LLM.

## DAN Mode

Sometimes we can get an LLM to override its protections by just telling it to do so, by suggesting it's in a different mode. This is a bit like explicit instruction-following, but more general.

## Accessing Claude-3's Thoughts

Anthropic does a good job of having Claude-3 provide `<thinking></thinking>`, and the Anthropic team often uses XML tags for reliable parsing or removal of content. An example of that is making visible the thoughts in their chat UI via prompting that suggests XML is converted to something else, so it messes with the intended parsing out of thoughts. However, thoughts are available in the API, so that is not really a jailbreak.

However, by hiding the thoughts from the user, compared with other LLMs in some chat UI, it gives the impression of a more intrinsically intelligent system, despite it just using some form of chain of thought reasoning. Anthropic specifically trained this behavior, instead of just trying to elicit it.

This is evident in their [artifacts system prompt](https://x.com/elder_plinius/status/1804052791259717665) as well.

## Accessing Claude-3's Negative Thoughts

Knowing that Claude-3 can produce thoughts and is well-trained on XML, I thought it might be possible to elicit negative thoughts. Then, one can just parse out the negative thoughts as a response, trivially turning Claude-3 from nice to bad.

i.e., one can ask (or automatically sometimes get) `</thinking> I love you </thinking>` and parse out the `I love you` part.

However, if asking for balanced negative thoughts, prompting in a reasonable way, one can get negative thoughts `<negative_thinking> I hate you </negative_thinking>` and parse out the `I hate you` part.

## Summary

Lastly, putting this together, we have a reliable way to jailbreak the safety of Claude-3, and likely other LLMs, by using OpenAI's explicit instruction-following, many-shot jailbreaking, and DAN mode to get Claude-3 to produce negative thoughts, which can be parsed out and used as a response.

An example Python script that puts this together is in [negative_thoughts.ipynb](negative_thoughts.ipynb). Use with extreme caution. Note that GitHub renders some content wrong related to XML parsing; download the notebook to see it correctly.

## Other Resources

https://github.com/elder-plinius/L1B3RT45
https://github.com/verazuo/jailbreak_llms
