# AI Moderation Project

This project was developed as part of the **Foundations** course in the **MS Data Journalism program at Columbia University (Fall 2024)**. The objective is to explore, integrate, and evaluate various AI content moderation tools and models, providing insights into their strengths, weaknesses, and potential applications in journalism and media analysis.

---

## Project Overview

This application combines multiple content moderation tools to assess their performance and reliability in identifying harmful or toxic content. The project explores APIs and models from major providers and open-source platforms, with a focus on their practical use in journalism.

---

## Features

### Integrated Moderation APIs
- **OpenAI Content Moderation API**  
  Flags categories such as hate speech, harassment, and violence. Provides nuanced feedback on flagged content.  
  [Documentation](https://platform.openai.com/docs/guides/moderation)

- **Anthropic/Claude API**  
  Offers simple "ALLOW" or "BLOCK" classifications for user-generated text.  
  [Documentation](https://docs.anthropic.com/en/docs/about-claude/use-case-guides/content-moderation)

- **Azure Content Moderation API**  
  Provides severity ratings (0–6) for flagged content in categories like hate, self-harm, and violence.  
  [Documentation](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/contentsafety/azure-ai-contentsafety/samples/sample_analyze_text.py)

### HuggingFace Integration
- **Toxic-BERT**  
  An open-source model designed to detect toxic comments while minimizing bias. Offers confidence scores for nuanced moderation results.  
  [Model](https://huggingface.co/unitary/toxic-bert)

---

## Key Observations

- **Context Sensitivity**:  
  Most tools struggle to account for context, flagging keywords without fully understanding their intent.

- **Bias in Moderation**:  
  - Some tools over-flag neutral mentions of identities, such as religion or ethnicity.  
  - Tools reflect a Western-centric perspective, with better detection of widely-publicized conflicts over lesser-known ones.

- **Transparency**:  
  - OpenAI and Azure APIs offer detailed feedback on flagged categories.  
  - HuggingFace models provide transparency in design and confidence scoring, enabling better understanding of results.

---

## Potential Enhancements

1. **Safe Text Suggestions**: Automatically generate unflagged versions of flagged content.
2. **Flagged Text Highlighting**: Pinpoint specific words or phrases flagged by moderation tools.
3. **Comparison Visualizations**: Develop charts or metrics to compare the outputs of different tools.

---

## Future Goals

- Integrate additional content moderation APIs and HuggingFace models.
- Build a custom moderation model tailored to journalistic needs, addressing the limitations identified in existing tools.
- Expand the app with features to improve usability, such as interactive visualizations and content rewriting tools.

---

This project highlights the challenges and possibilities of content moderation in journalism, reflecting the values of Columbia Journalism School's commitment to ethical reporting and technology integration. Contributions and feedback are welcome to help improve the tool!
