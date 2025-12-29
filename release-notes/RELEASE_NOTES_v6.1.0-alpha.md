# MLOS Core v6.1.0-alpha Release Notes

**Release Date:** December 29, 2025
**Type:** Minor Alpha Release

---

## Highlights

This release enhances the kernel statistics and monitoring capabilities introduced in v6.0.0, adding comprehensive observability for NUMA memory allocation, prefetch learning adaptation, and prediction accuracy tracking.

---

## New Features

### 1. NUMA Memory Pool Statistics (#66)

**Impact:** Enhanced visibility into multi-socket memory utilization

New statistics tracking for NUMA-aware tensor allocation, providing per-node metrics for memory management optimization.

**New API:**
- `MLOS_NUMA_STATS` (ioctl 70) - Retrieve per-NUMA-node memory statistics

**Statistics provided:**
- `total_size` / `used_size` - Memory allocation per node
- `tensor_count` - Number of tensors on each node
- `allocations` - Total allocation count per node
- `remote_accesses` - Cross-node memory access count
- `migrations_in` / `migrations_out` - Tensor migration tracking

---

### 2. Adaptive Prefetch Learning (#66)

**Impact:** Self-tuning prefetch timing for optimal performance

Real-time tracking of prefetch learning state, enabling visibility into how the system adapts prefetch timing based on actual execution patterns.

**New API:**
- `MLOS_PREFETCH_LEARNING` (ioctl 71) - Get/set prefetch learning state

**Learning metrics:**
- `learning_enabled` - Whether adaptive learning is active
- `learning_rate_pct` - Current learning rate
- `offset_adjustment_ns` - Timing adjustment applied
- `successful_prefetches` / `late_prefetches` / `early_prefetches` - Timing accuracy
- `avg_timing_error_ns` - Average timing deviation

---

### 3. Enhanced Prediction Accuracy Tracking (#66)

**Impact:** Quantifiable prediction quality metrics

Comprehensive tracking of deadline prediction accuracy, enabling users to assess and tune prediction reliability.

**New API:**
- `MLOS_PREDICTION_ACCURACY` (ioctl 72) - Get prediction accuracy stats

**Accuracy metrics:**
- `total_predictions` - Total predictions made
- `accurate_predictions` - Predictions within tolerance
- `missed_predictions` - False negatives (predicted success, actually missed)
- `false_alarms` - False positives (predicted miss, actually succeeded)
- `accuracy_pct` - Overall accuracy percentage
- `calibration_score` - Prediction confidence calibration
- `avg_prediction_error_ns` / `variance_ns` - Error distribution

---

### 4. Extended Latency Statistics (#66)

**Impact:** Statistical analysis for latency optimization

Extended latency tracking with variance, standard deviation, and percentile calculations for detailed performance analysis.

**New API:**
- `MLOS_MODEL_LATENCY_EXT` (ioctl 73) - Get extended latency statistics

**Extended metrics:**
- `p50_duration_ns` / `p90_duration_ns` / `p99_duration_ns` - Percentiles
- `variance_ns` / `stddev_ns` - Statistical dispersion
- `outlier_count` - Samples beyond 3 standard deviations

---

## Technical Details

### Files Changed
- `kernel/include/mlos_ioctl.h` - New ioctl definitions (70-73)
- `kernel/include/mlos_kernel.h` - Configuration cleanup
- `kernel/include/mlos_sched.h` - Extended stats structures
- `kernel/include/mlos_tensor.h` - NUMA pool stats structures
- `kernel/mlos_chardev.c` - New ioctl handlers
- `kernel/mlos_sched.c` - Extended latency implementation
- `kernel/mlos_tmm.c` - NUMA statistics implementation

### Ioctl Summary
| Ioctl | Number | Description |
|-------|--------|-------------|
| `MLOS_NUMA_STATS` | 70 | NUMA memory pool statistics |
| `MLOS_PREFETCH_LEARNING` | 71 | Prefetch learning state |
| `MLOS_PREDICTION_ACCURACY` | 72 | Prediction accuracy metrics |
| `MLOS_MODEL_LATENCY_EXT` | 73 | Extended latency statistics |

Total ioctls: 23 (up from 19 in v6.0.0)

### Breaking Changes
None - all changes are additive and backward compatible.

---

## Upgrade Path

1. Update to v6.1.0-alpha (standard package upgrade)
2. Existing applications work without modification
3. Optionally query new statistics for observability:
   - Use `MLOS_NUMA_STATS` to monitor memory distribution
   - Use `MLOS_PREFETCH_LEARNING` to track prefetch adaptation
   - Use `MLOS_PREDICTION_ACCURACY` to assess prediction quality
   - Use `MLOS_MODEL_LATENCY_EXT` for detailed latency analysis

---

## Known Issues

- Extended latency percentiles are approximated from available samples
- NUMA statistics require multi-socket hardware
- Prefetch learning metrics reflect plan-level, not per-tensor data

---

## Contributors

- MLOS Foundation Team

---

*For detailed API documentation, see the kernel header files.*
