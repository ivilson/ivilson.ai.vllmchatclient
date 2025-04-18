# ivilson.ai.vllmchatclient

It can work for qwq32b, gemma3,glm4,glm z1 on vllm==v0.8.4.
use class VllmQwqChatClient ,VllmGemmaChatClient, VllmGlm4ChatClient , VllmGlmZ1ChatClient

support stream function call .


```
string apiurl = "http://localhost:8000/{0}/{1}";

[Description("Gets the weather")]
static string GetWeather() => Random.Shared.NextDouble() > 0.5 ? "It's sunny" : "It's raining";

IChatClient vllmclient = new VllmQwqChatClient(apiurl,null, "qwq");
ChatOptions chatOptions = new()
{
    Tools = [AIFunctionFactory.Create(GetWeather)]
};
var messages = new List<ChatMessage>
{
    new ChatMessage(ChatRole.System ,"你是一个智能助手，名字叫菲菲"),
    new ChatMessage(ChatRole.User,"今天天气如何？")
};

private async Task<(string answer, string reasoning)> StreamChatResponseAsync(List<ChatMessage> messages, ChatOptions chatOptions)
{
    string answer = string.Empty;
    string reasoning = string.Empty;
    await foreach (var update in _chatClient.GetStreamingResponseAsync(messages, chatOptions))
    {
        var updateText = update.ToString();
        if (update is ReasoningChatResponseUpdate reasoningUpdate)
        {
            if (!reasoningUpdate.Thinking)
            {
                answer += updateText;
            }
            else
            {
                reasoning += updateText;
            }
        }
        else
        {
            answer += updateText;
        }
    }
    return (answer, reasoning);
}

var (answer, reasoning) = await StreamChatResponseAsync(messages, chatOptions);
```
