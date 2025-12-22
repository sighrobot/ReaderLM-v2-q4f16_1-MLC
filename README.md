# ReaderLM-v2-q4f16_1-MLC

This is [jinaai/ReaderLM-v2](https://huggingface.co/jinaai/ReaderLM-v2) compiled for WebLLM/MLC-LLM with q4f16_1 quantization.

## Model Details

- **Base Model**: jinaai/ReaderLM-v2 (based on Qwen2.5-1.5B-Instruct)
- **Quantization**: q4f16_1 (4-bit weights, 16-bit activations)
- **Context Window**: 8192 tokens
- **Total Size**: ~850MB

## Usage with WebLLM

```typescript
import * as webllm from "@mlc-ai/web-llm";

const MODEL_ID = "ReaderLM-v2-q4f16_1-MLC";
const MODEL_URL = "https://huggingface.co/YOUR_USERNAME/ReaderLM-v2-q4f16_1-MLC/resolve/main";

const engine = await webllm.CreateMLCEngine(MODEL_ID, {
  appConfig: {
    model_list: [
      {
        model_id: MODEL_ID,
        model: MODEL_URL,
        model_lib: `${MODEL_URL}/model.wasm`,
      },
    ],
  },
});

const response = await engine.chat.completions.create({
  messages: [{ role: "user", content: "Your prompt here" }],
  temperature: 0.3,
});

console.log(response.choices[0].message.content);
```

## Files

- `model.wasm` - Compiled WebGPU model
- `params_shard_*.bin` - Quantized model weights (30 shards)
- `mlc-chat-config.json` - Model configuration
- `tokenizer.json`, `vocab.json`, `merges.txt` - Tokenizer files

## Compilation

This model was compiled using MLC-LLM with the following settings:

```bash
# Convert weights
python -m mlc_llm convert_weight ./ReaderLM-v2 --quantization q4f16_1 --output ./dist

# Generate config
python -m mlc_llm gen_config ./ReaderLM-v2 --quantization q4f16_1 --conv-template chatml --context-window-size 8192 --output ./dist

# Compile for WebGPU
python -m mlc_llm compile ./dist --device webgpu --output ./dist/model.wasm
```

## License

Same as the original ReaderLM-v2 model.
