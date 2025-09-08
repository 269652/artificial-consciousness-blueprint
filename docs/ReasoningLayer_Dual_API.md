# ReasoningLayer Dual API Support

The ReasoningLayer now supports both Perplexity and DeepSeek APIs for enhanced flexibility and redundancy.

## Configuration

### Environment Variables

Add these to your `.env` file:

```bash
# Choose your reasoning provider: "perplexity" or "deepseek"
REASONING_PROVIDER=perplexity

# Perplexity API Configuration
PERPLEXITY_API_KEY=your_perplexity_api_key_here
PERPLEXITY_MODEL=sonar-pro

# DeepSeek API Configuration  
DEEPSEEK_API_KEY=your_deepseek_api_key_here
DEEPSEEK_MODEL=deepseek-chat
```

### Switching Providers

To use **Perplexity** (default):
```bash
REASONING_PROVIDER=perplexity
PERPLEXITY_API_KEY=your_key_here
```

To use **DeepSeek**:
```bash
REASONING_PROVIDER=deepseek
DEEPSEEK_API_KEY=your_key_here
```

## Manual Initialization

You can also create ReasoningLayer instances manually:

```python
from src.modules.ReasoningLayer import ReasoningLayer

# Perplexity
reasoning_layer = ReasoningLayer(model_name="sonar-pro", provider="perplexity")

# DeepSeek
reasoning_layer = ReasoningLayer(model_name="deepseek-chat", provider="deepseek")
```

## Factory Function

For automatic configuration based on environment variables:

```python
from src.config.reasoning_config import create_reasoning_layer

# Creates ReasoningLayer with provider from REASONING_PROVIDER env var
reasoning_layer = create_reasoning_layer()
```

## Performance Optimization

The ReasoningLayer now includes:

1. **Merged API Calls**: Combined cleaning and reasoning into single API call (50% reduction in API requests)
2. **Dual Provider Support**: Fallback capability between Perplexity and DeepSeek
3. **Unified Error Handling**: Consistent error handling across both providers
4. **Provider-Specific Optimizations**: Each provider gets optimized request parameters

## Usage in Components

All consciousness components (ThoughtLayer, InnerSpeech, etc.) automatically use the configured provider:

- `ThoughtLayer` - Main thought processing
- `InnerSpeech` - Reflective thought generation
- `SeedExperience` - Personality response generation

## Benefits

1. **Redundancy**: Switch providers if one is unavailable
2. **Cost Optimization**: Use different providers based on pricing
3. **Performance**: Compare response quality between providers
4. **Compliance**: Meet different regional API requirements

## API Compatibility

Both providers support the same interface:
- Chat completions endpoint
- Message format
- Temperature and top_p parameters
- Max tokens limiting

The unified `_api_generate()` method handles provider-specific differences automatically.
