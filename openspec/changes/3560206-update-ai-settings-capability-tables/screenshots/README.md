# QA Screenshots

Screenshots captured during QA testing of the AI Settings page reorganization (Issue #3560206).

## Screenshots

### 01-empty-state-no-providers.png

**State**: No AI providers configured
**URL**: `/admin/config/ai/settings`

Verified:
- "AI Capabilities from Installed Providers" section shows empty state with link to AI Provider documentation
- "Vector Data Capabilities" section with two h2 subsections:
  - **Embedding Providers**: Shows install links (OpenAI, Azure, Ollama, AWS Bedrock, Huggingface, Vertex AI, Fireworks AI, LMStudio)
  - **Vector Database (VDB) Providers**: Shows install links (Milvus, Pinecone, PostgreSQL, SQLite)
- No empty tables displayed - only install prompts with provider links
- All install links verified to return HTTP 200

### 02-openai-provider-validation.png

**State**: OpenAI provider enabled, attempting to configure with test API key
**URL**: `/admin/config/ai/providers/openai`

Verified:
- Provider configuration form validates API key before saving
- Error message displayed when invalid key is used: "The selected API key is not working. Please double-check the correct API key was entered and that it has credit(s) available."

## Test Notes

### What Was Tested

1. **Empty State (No Providers)**: Confirmed all three sections display correctly with appropriate install prompts
2. **Install Links**: All 12 provider links verified (8 embedding + 4 VDB)
3. **Provider Validation**: OpenAI provider correctly validates API keys

### Not Tested (Requires Valid API Key)

- Populated state with capability tables showing provider/model dropdowns
- AJAX model dropdown updates
- Form submission with valid provider configuration

To test the populated state, configure OpenAI (or another provider) with a valid API key, then revisit `/admin/config/ai/settings`.
