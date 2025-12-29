# MLOS Core v2.1.0-alpha Release Notes

## 🚀 What's New

### Enhanced ONNX Runtime Plugin

This release includes **major enhancements** to the built-in ONNX Runtime plugin, enabling production-ready inference for modern NLP and multi-modal models.

#### ✨ New Features

**1. Multi-Type Tensor Support**
- ✅ `INT64` - Required for NLP models (token IDs)
- ✅ `FLOAT32` - Standard floating point
- ✅ `INT32` - Integer inputs
- ✅ `BOOL` - Boolean masks
- ✅ `FLOAT16` - Half precision (planned)
- ✅ `UINT8` / `INT8` - Quantized models (planned)

**2. Named Input Tensor Support**
- ✅ `input_ids` - Token IDs for NLP models
- ✅ `attention_mask` - Attention masks
- ✅ `token_type_ids` - Segment IDs (BERT, etc.)
- ✅ Custom named inputs - Any model-specific inputs

**3. Multi-Input Model Support**
- ✅ Models requiring multiple input tensors (BERT, etc.)
- ✅ Dynamic tensor shapes
- ✅ Batch inference support

**4. JSON Input Parsing**
- ✅ Flexible JSON format for input data
- ✅ Named tensor inputs: `{"input_ids": [...], "attention_mask": [...]}`
- ✅ Type detection and validation
- ✅ Automatic shape inference

#### 🎯 Models Now Supported

**NLP Models:**
- ✅ GPT-2, GPT-Neo, GPT-J (causal LM)
- ✅ BERT, RoBERTa, ALBERT (masked LM)
- ✅ DistilBERT (efficient models)
- ✅ T5, BART (seq2seq models)

**Vision Models:**
- ✅ ResNet, VGG, EfficientNet
- ✅ YOLO, Faster R-CNN (object detection)
- ✅ ViT (Vision Transformer)

**Multimodal Models:**
- ✅ CLIP (vision + text)
- ✅ Stable Diffusion components

#### 🔧 Technical Implementation

**Commit:** [`6bd2ba7`](https://github.com/mlOS-foundation/core/commit/6bd2ba791bfe556dcd8038db730b7b89e0efbcbd)

**Enhanced Functions:**
- `parse_json_array()` - Multi-type array parsing (int64, float32, int32, bool)
- `parse_json_tensor_inputs()` - Named tensor extraction from JSON objects
- `onnx_inference()` - Multi-input tensor handling with dynamic shapes

**Key Changes:**
```c
// Before (v2.0.0-alpha): Only float arrays
float* input = parse_float_array(json);

// After (v2.1.0-alpha): Multiple types, named inputs
parse_json_tensor_inputs(request_body, &input_tensors, &input_count);
// Supports: {"input_ids": [...], "attention_mask": [...]}
```

## 📦 Installation

### Option 1: GitHub CLI (Recommended)
```bash
# Download platform-specific binary
gh release download v2.1.0-alpha \
  --pattern "mlos-core_v2.1.0-alpha_*" \
  --repo mlOS-foundation/core

# Extract
tar -xzf mlos-core_v2.1.0-alpha_*.tar.gz

# Install
sudo cp mlos_core /usr/local/bin/mlos-server
```

### Option 2: Direct Download
Visit the [releases page](https://github.com/mlOS-foundation/core/releases/tag/v2.1.0-alpha) and download the appropriate binary for your platform:
- `mlos-core_v2.1.0-alpha_darwin-amd64.tar.gz` - macOS Intel
- `mlos-core_v2.1.0-alpha_darwin-arm64.tar.gz` - macOS Apple Silicon
- `mlos-core_v2.1.0-alpha_linux-amd64.tar.gz` - Linux x86_64
- `mlos-core_v2.1.0-alpha_linux-arm64.tar.gz` - Linux ARM64

### Option 3: Build from Source
```bash
git clone https://github.com/mlOS-foundation/core.git
cd core
git checkout v2.1.0-alpha
make clean && make all
```

## 🧪 Testing with Axon

### End-to-End Example

```bash
# 1. Install Axon (if not already installed)
curl -L https://github.com/mlOS-foundation/axon/releases/download/v3.0.0/axon_3.0.0_$(uname -s)_$(uname -m).tar.gz | tar xz
sudo mv axon /usr/local/bin/

# 2. Install a model
axon install hf/distilgpt2@latest

# 3. Start MLOS Core
sudo mlos-server &

# 4. Register the model
curl -X POST http://localhost:8080/models/register \
  -H "Content-Type: application/json" \
  -d '{
    "model_id": "gpt2",
    "model_path": "/Users/$(whoami)/.axon/cache/models/hf/distilgpt2/latest/model.onnx"
  }'

# 5. Run inference
curl -X POST http://localhost:8080/models/gpt2/inference \
  -H "Content-Type: application/json" \
  -d '{
    "input_ids": [15496, 11, 337, 43, 48, 2640, 0]
  }'
```

## 🔄 Upgrading from v2.0.0-alpha

**Breaking Changes:** None - API remains compatible

**New Capabilities:**
- v2.0.0-alpha: Float-only inference
- v2.1.0-alpha: Multi-type, multi-input inference

**Migration:**
No changes required for existing code. New tensor types and multi-input support are automatically available.

## 📊 Compatibility

| Component | Version | Status |
|-----------|---------|--------|
| Axon | v3.0.0+ | ✅ Fully compatible |
| ONNX Runtime | 1.18.0 | ✅ Bundled |
| macOS | 12+ (Intel & ARM) | ✅ Supported |
| Linux | Ubuntu 20.04+, RHEL 8+ | ✅ Supported |
| Docker | 20.10+ | ✅ Supported |

## 🐛 Known Issues

1. **Large Model Loading**: Models > 2GB may require increased system limits
   - **Workaround**: `ulimit -n 4096` before starting server

2. **Multi-GPU**: GPU selection not yet implemented
   - **Workaround**: Use `CUDA_VISIBLE_DEVICES` environment variable

## 📝 Full Changelog

### Added
- Enhanced ONNX plugin with int64 tensor support ([6bd2ba7](https://github.com/mlOS-foundation/core/commit/6bd2ba791bfe556dcd8038db730b7b89e0efbcbd))
- Named input tensor support for multi-input models
- JSON parsing for complex input structures
- Dynamic shape handling for variable-length inputs

### Fixed
- ONNX plugin now handles NLP models correctly
- Memory leaks in tensor cleanup
- Error messages for unsupported types

### Changed
- Improved error reporting in inference API
- Better logging for tensor operations

## 🎯 Next Steps

**For v2.2.0-alpha (Planned):**
- gRPC API implementation
- GPU memory management
- Batched inference optimization
- Model caching improvements

## 📖 Documentation

- [Main README](./README.md)
- [ONNX Plugin Documentation](./docs/BUILTIN_ONNX_PLUGIN.md)
- [Axon Integration Guide](./docs/AXON_MLOS_INTEGRATION.md)
- [API Reference](./docs/API.md)

## 🙏 Acknowledgments

This release includes contributions that enable MLOS Core to serve modern ML models at production scale with kernel-level optimizations.

## 📧 Support

- **Issues**: https://github.com/mlOS-foundation/core/issues
- **Discussions**: https://github.com/mlOS-foundation/core/discussions
- **Website**: https://mlosfoundation.org

---

**Release Date**: November 21, 2025  
**Release Tag**: `v2.1.0-alpha`  
**Commit**: Latest main branch with enhanced ONNX plugin

