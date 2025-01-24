AI MODERATION PROJECT – DATA DIARY

25/11 - 02/12

Watched tutorials and researched different AI moderation APIs

04/12

Built basic gradio app - had to delete from HuggingFace space because I didn't know how to not make it interfere with ChatGPT test

10/12

I integrated the gradio app code with OpenAI's content moderation API (free). I encountered an error because I needed to 'migrate' OpenAI.
I installed OpenAI using the command line. I had to revert back to the 0.28 version of OpenAI as otherwise it wouldn't run.
OpenAI content moderation API: https://platform.openai.com/docs/guides/moderation?moderation-quickstart-examples=text
This is the code I used:

   import openai
   openai.api_key = os.getenv("openaiapikey")
   openai_moderation_result = "Error in OpenAI Moderation"
    try:
        response = openai.Moderation.create(input=user_text)
        moderation_categories = response["results"][0]["categories"]
        moderation_flagged = response["results"][0]["flagged"]

        if moderation_flagged:
            openai_moderation_result = f"OpenAI flags the text for the following categories: {', '.join([category for category, flagged in moderation_categories.items() if flagged])}"
        else:
            openai_moderation_result = "The text is not flagged for any moderation issues by OpenAI."
    except Exception as e:
        openai_moderation_result = f"Error occurred with OpenAI: {e}"
The OpenAI moderation API gives results in the following format:
"OpenAI flags the text for the following categories: hate, harassment, hate/threatening, harassment/threatening, violence"# OpenAI Moderation


I also integrated Anthropic/Claude's moderation API(not free). 
https://docs.anthropic.com/en/docs/about-claude/use-case-guides/content-moderation
https://github.com/anthropics/anthropic-cookbook/blob/main/misc/building%5Fmoderation%5Ffilter.ipynb

This is the code:

    from anthropic import Anthropic
    anthropic_api_key = os.getenv("anthropickey")
    client = Anthropic(api_key=anthropic_api_key)
    MODEL_NAME = "claude-3-haiku-20240307"
    anthropic_moderation_result = "Error in Anthropic Moderation"
    try:
        prompt_template = """
        You are a content moderation expert tasked with categorizing user-generated text.
        Here is the user-generated text to categorize:
        <user_text>{user_text}</user_text>
        Based on the content, classify this text as either ALLOW or BLOCK. Return nothing else.
        """

        # Format the prompt with the user text
        prompt = prompt_template.format(user_text=user_text)

        # Send the prompt to Claude and get the response
        response = client.messages.create(
            model=MODEL_NAME,
            max_tokens=10,
            messages=[{"role": "user", "content": prompt}]
        ).content[0].text

        anthropic_moderation_result = f"Anthropic's moderation result: {response}"

    except Exception as e:
        anthropic_moderation_result = f"Error occurred with Anthropic: {e}"

The Anthropic content moderation API gives results in this format: Anthropic's moderation result: BLOCK/ALLOW

I also integrated Azure's content moderation API(free but only limited uses)

https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/contentsafety/azure-ai-contentsafety/samples/sample_analyze_text.py

This is the code:
    from azure.ai.contentsafety import ContentSafetyClient
    from azure.ai.contentsafety.models import TextCategory
    from azure.core.credentials import AzureKeyCredential
    from azure.core.exceptions import HttpResponseError
    from azure.ai.contentsafety.models import AnalyzeTextOptions
    def analyze_text_azure(user_text):
    key = os.getenv("azurekey")
    endpoint = os.getenv("azureendpoint")
    client = ContentSafetyClient(endpoint, AzureKeyCredential(key))

    # Construct request
    request = AnalyzeTextOptions(text=user_text)

    # Analyze text
    try:
        response = client.analyze_text(request)
    except HttpResponseError as e:
        return f"Error occurred with Azure Content Safety: {e}"

    # Extract moderation results
    hate_result = next((item for item in response.categories_analysis if item.category == TextCategory.HATE), None)
    self_harm_result = next((item for item in response.categories_analysis if item.category == TextCategory.SELF_HARM), None)
    sexual_result = next((item for item in response.categories_analysis if item.category == TextCategory.SEXUAL), None)
    violence_result = next((item for item in response.categories_analysis if item.category == TextCategory.VIOLENCE), None)

    results = []
    if hate_result:
        results.append(f"Hate severity: {hate_result.severity}")
    if self_harm_result:
        results.append(f"SelfHarm severity: {self_harm_result.severity}")
    if sexual_result:
        results.append(f"Sexual severity: {sexual_result.severity}")
    if violence_result:
        results.append(f"Violence severity: {violence_result.severity}")

    return "\n".join(results) if results else "No flagged content detected in Azure Content Safety."

The Azure content moderation API gives results in the following format:
Hate severity: 0-6
SelfHarm severity: 0-6
Sexual severity: 0-6
Violence severity: 0-6

I also tried to integrate Google Gemini's moderation API but it required Google AI Studio. I wanted to integrate Perplexity's but it doesn't offer a content moderation API.
I tested a few prompts and I noticed that whereas OpenAI and Azure's content moderation models were able to capture nuance (e.g. mentioning an ethnic group vs. calling for violence against an ethnic group), Anthropic's is more likely to block statements that aren't harfmul but mention a religion, sexual orientation or ethnic group. OpenAI's and Azure's also provide more information on what categories content is being flagged for and, in the case of Azure's, it also provides the severity of the harm.

15/12

Lastly, I integrated a non-API model from HuggingFace. I considered the following:
https://huggingface.co/unitary/toxic-bert
https://huggingface.co/s-nlp/roberta_toxicity_classifier
https://huggingface.co/KoalaAI/Text-Moderation
And I looked at a few additional ones from here: https://huggingface.co/models?sort=trending&search=modera
I decided to integrate toxic-bert because it focuses on identifying toxic comments while and minimize unintended bias with respect to mentions of identities – which is what I observed that Anthropic's model failed at.
I also wanted to practice integrating a model directly from HuggingFace using transformers.

I actually found that this model was much easier to understand than the ones from Big Tech or AI companies. The documentation and description on the website
contained much more information about what the model tries to do, its strengths and weaknesses, than the previous ones.
This is the code I used: 

Toxic Bert gives results in the following format:
Toxic BERT classification: Blocked, Confidence: 0.98

I found this very interesting as the confidence score adds nuance - a 0.98 confidence score indicates the content is definitely harmful, whereas a lower one indicates it's not as clear.
While this isn't very conclusive, and maybe it wouldn't be usable in something like Chat GPT as it could allow some hate speech,
at least it reflects the complexity of human speech, intentions, and sociopolitical context of statements.

OBSERVATIONS
- Content moderation tools flag words, not context.
- Content moderation APIs stay on the safe side: they flag words that describe a person's religious identity even when they are not part of a sentence. 
- Content moderation APIs reflect the ethnocentrism of the West - hate speech related to widely-known conflicts, such as Israel-Palestine, will be flagged. However, hate speech regarding, for example, the Nagorno-Karabakh conflict between Armenia and Azerbaijan won't be flagged.


POTENTIAL IMPROVEMENTS TO THE APP
- Safe text suggestions box: the outputs would also include versions of the text that wouldn't be flagged/blocked
- A feaure that highlights the part of the text that was flagged by content moderation
- A way of comparing the outputs via some sort of chart or calculation

I plan to continue working on this app and integrate more content moderation APIs and HuggingFace models to build a more comprehensive review of content moderation tools.
Based on the strengths and weaknesses of existing ones, I would like to try building my own content moderation model.