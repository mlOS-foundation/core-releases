# MLOS Core v1.0.13-alpha Release Notes

## Overview

This alpha release introduces a **robust Docker-based build system** that ensures perfect parity between local development and CI environments. This eliminates the "works locally but fails in CI" problem by using the same Docker-based validation everywhere. The release also includes graceful shutdown improvements and Linux compatibility fixes.

## What's New

### 🐳 Docker-Based Build System
- **Multi-architecture support**: Build for `linux/amd64` and `linux/arm64` from a single machine
- **Docker validation**: Run all CI checks locally in Docker containers
- **Perfect parity**: Local validation matches CI exactly using the same scripts
- **Portable development**: Develop on macOS, test on Linux (catches Linux-specific issues)

### 📦 New Scripts and Tools
- `docker-build-all.sh`: Multi-architecture builds using Docker Buildx
- `docker-validate.sh`: Comprehensive validation in Docker
- `docker-validate-single.sh`: Single platform/compiler validation (used by CI)
- `docker-compose.yml`: Easy local development setup

### 🔧 Enhanced CI/CD
- CI workflows now use Docker-based validation
- Same validation script (`docker-validate-single.sh`) used locally and in CI
- `create-pr.sh` automatically uses Docker validation if available
- Multi-architecture Docker build tests in CI

## Improvements

### 🛠️ Build System
- **Consistent environments**: Same Docker environment locally and in CI
- **Faster feedback**: Catch Linux-specific issues before PR creation
- **Better debugging**: Detailed build logs and error messages
- **Cross-platform**: Build for multiple architectures from single machine

### 🚀 Developer Experience
- **Automatic validation**: `create-pr.sh` uses Docker validation automatically
- **Clear error messages**: Better feedback when validation fails
- **Easy testing**: `make docker-validate-ci` runs exact CI validation locally
- **Documentation**: Complete guide in `docs/DOCKER_BUILD_SYSTEM.md`

### 🔄 Graceful Shutdown
- Improved signal handling using `sigaction()` for robust signal handling
- Non-blocking socket `accept()` calls for interruptible server threads
- Proper cleanup sequence: stop threads before cleanup
- Fixed zombie processes on macOS

### 🐧 Linux Compatibility
- Added `_POSIX_C_SOURCE` for `sigaction` on Linux
- Fixed compilation issues that were missed on macOS
- Better cross-platform compatibility

## Fixes

### 🐛 Bug Fixes
- **Graceful shutdown**: Fixed processes not terminating cleanly (zombie processes)
- **Linux compilation**: Fixed `sigaction` compilation error on Linux
- **Signal handling**: Replaced `signal()` with `sigaction()` for reliability
- **Socket blocking**: Made HTTP server `accept()` non-blocking for graceful shutdown

### 🔒 Security
- **Zip Slip vulnerability**: Fixed path traversal in archive extraction (Axon)
- **Path sanitization**: Improved archive extraction security

## Technical Details

### Docker Build System Architecture

The Docker-based build system uses:
- **Multi-stage builds**: Builder stage for compilation, production stage for runtime
- **Docker Buildx**: For multi-architecture support
- **Consistent validation**: Same Dockerfile and scripts for all environments

### CI Integration

CI workflows now:
1. Use `docker-validate-single.sh` for all test jobs
2. Build multi-architecture Docker images
3. Use Docker builds for CodeQL security analysis
4. Ensure perfect parity with local validation

### Validation Flow

```
Local Development:
  create-pr.sh → docker-validate-ci → docker-validate-single.sh (gcc, clang)

CI Pipeline:
  CI workflow → docker-validate-single.sh (gcc, clang)
  
Result: Perfect parity between local and CI
```

## Breaking Changes

None. This is a backward-compatible release.

## Migration Guide

### For Developers

1. **Install Docker** (if not already installed):
   ```bash
   # macOS
   brew install docker
   
   # Linux
   sudo apt-get install docker.io
   ```

2. **Use Docker validation** (recommended):
   ```bash
   # Before creating PR
   make docker-validate-ci
   
   # Or use create-pr.sh (automatically uses Docker)
   ./scripts/create-pr.sh --title "..." --body "..."
   ```

3. **Build Docker images**:
   ```bash
   # Build for local platform
   make docker-build
   
   # Build for all architectures
   make docker-build-all
   ```

### For CI/CD

No changes needed. CI workflows automatically use Docker-based validation.

## Testing

### Test Docker Build System

```bash
# 1. Test local Docker build
make docker-build
make docker-test

# 2. Test validation (matches CI)
make docker-validate-ci

# 3. Test multi-architecture builds
make docker-build-all

# 4. Test create-pr.sh with Docker
./scripts/create-pr.sh --title "Test" --body "Testing Docker validation"
```

### Test Graceful Shutdown

```bash
# Start MLOS Core
./build/mlos_core

# Send SIGTERM (Ctrl+C or kill -TERM)
# Verify clean shutdown (no zombie processes)
```

### Test Linux Compatibility

```bash
# Run in Docker (Linux environment)
docker run -it --rm -v $(pwd):/build -w /build ubuntu:22.04 bash
apt-get update && apt-get install -y build-essential gcc make
make clean && make
```

## Performance

- **Docker builds**: Slightly slower initial build (downloads base image)
- **Validation**: Same speed as native (runs in Docker)
- **CI**: Faster feedback (catches issues before PR)

## Known Issues

- Docker required for full validation (falls back to local validation if unavailable)
- ARM64 builds require Docker Buildx (automatically set up)

## Contributors

- Development team: Docker build system implementation
- CI/CD improvements: Workflow enhancements
- Testing: Validation and testing improvements

## Next Steps

- Continue using Docker-based validation for all PRs
- Monitor CI performance with Docker builds
- Consider adding more architectures (ppc64le, s390x) if needed
- Optimize Docker image size (consider distroless/alpine)

## References

- [Docker Build System Documentation](docs/DOCKER_BUILD_SYSTEM.md)
- [CI Workflow](.github/workflows/ci.yml)
- [Docker Validation Script](scripts/docker-validate-single.sh)

---

**Release Date**: 2025-11-17  
**Version**: v1.0.13-alpha  
**Repository**: https://github.com/mlOS-foundation/core

