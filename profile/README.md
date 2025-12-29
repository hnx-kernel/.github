# HNX Operating System

## Overview

HNX (Hybrid N Unix) is a modern hybrid-kernel operating system written in Rust. It combines the security benefits of microkernels with the performance advantages of monolithic kernels, featuring a novel capability-based security model and POSIX compatibility.

**Key Features:**
- **Hybrid Kernel Architecture:** Core services in user space with microkernel foundation
- **Capability Security:** Fine-grained access control through capabilities
- **Modern Memory Safety:** Built entirely in Rust for memory safety guarantees
- **Cross-Platform Support:** Native aarch64 support with plans for x86_64 and RISC-V
- **POSIX Compatibility:** Full support for existing Unix/Linux ecosystem
- **ohlink Binary Format:** Innovative executable format with built-in security

## Quick Start

### Prerequisites
- **Rust:** Nightly toolchain (specified in `rust-toolchain.toml`)
- **QEMU:** System emulator for aarch64 (`qemu-system-aarch64`)
- **Cross-compilation:** AArch64 target (`aarch64-unknown-none`)
- **Python 3.11+:** Build script dependencies

### Building HNX
```bash
# Display available build targets
make help

# Configure the build environment
make configure

# Build everything (kernel + userspace)
make all

# Create system image
make image

# Run in QEMU with 30-second timeout
make run-simple

# Debug with GDB
make debug
```

### Development Workflow
```bash
# 1. Configure for your target
make configure ARCH=aarch64 BOARD=qemu-virt PROFILE=debug

# 2. Build kernel components
make kernel

# 3. Build userspace services
make space

# 4. Test with quick image
make simple-image
make run-simple

# 5. Check system logs during execution
# (Kernel output appears in QEMU console)
```

## Project Status

### ✅ Implemented
- Boot process from reset vector to kernel entry
- Physical/virtual memory management (4-level page tables)
- Basic process management with EL0/EL1 privilege switching
- VFS layer with RamFS and DevFS
- Serial console and logging system
- Three-layer syscall architecture
- Service-based IPC infrastructure
- Unified service development framework

### 🔄 In Progress
- Interrupt system (GIC, timer, exception vectors)
- System call interface and IPC mechanisms
- Scheduler with basic Round-Robin algorithm
- Userland C runtime and simple shell
- ohlink binary format specification
- Service health monitoring

### 📅 Planned
- Migration to hybrid architecture (userspace services)
- Capability-based security model implementation
- TCP/IP network stack from scratch
- Multi-architecture support (x86_64, RISC-V)
- Device driver framework

## Architecture

### Layered Design
```
┌─────────────────────────────────────────┐
│         Userspace Applications          │
│          (ohlink format, capabilities)  │
├─────────────────────────────────────────┤
│           BSD Compatibility Layer       │
├─────────────────────────────────────────┤
│   Userspace Services (FS, Net, Drivers) │
├─────────────────────────────────────────┤
│   Microkernel Core (IPC, VM, Scheduler) │
├─────────────────────────────────────────┤
│      HAL (aarch64, Driver Framework)    │
└─────────────────────────────────────────┘
```

### Key Components
- **Microkernel Core:** Minimal kernel providing IPC, memory management, scheduling
- **Userspace Services:** Core functionality as isolated services (Loader, VFS, etc.)
- **Capability System:** All resource access controlled through capabilities
- **Three-layer Syscalls:** Userspace → Kernel → Service delegation architecture

## Directory Structure

```
hnx-core/
├── .cargo/                    # Cargo configuration
├── configs/                   # Build configurations
│   ├── arch/                 # Architecture-specific configs
│   ├── board/                # Board-specific configs
│   ├── profile/              # Build profiles
│   └── scripts/              # Configuration scripts
├── include/                   # C headers (ABI definitions)
│   └── hnx/abi/              # System ABI headers
├── scripts/                   # Build and utility scripts
│   ├── create-image.py       # System image creation
│   ├── version.py            # Version management
│   └── verify_version.py     # Version consistency checks
├── src/                       # Source code
│   ├── abi-bindings/         # Rust ABI bindings
│   ├── kernel/               # Kernel core (library crate)
│   ├── space/                # Userspace components (workspace)
│   │   ├── hnxlib/           # HNX system library
│   │   ├── libc/             # C standard library
│   │   └── services/         # System services
│   └── utils/                # Development utilities
├── targets/                   # Rust target definitions
├── tests/                    # Test framework
├── tools/                    # Development tools
└── docs/                     # Documentation
```

## For Specific Users

### New Kernel Developer
Welcome to HNX development! Start here:

* **Getting Started:** [docs/development/getting-started.md](docs/development/getting-started.md)
* **First Service:** [docs/guides/first-service.md](docs/guides/first-service.md)
* **Syscall Development:** [docs/development/syscalls.md](docs/development/syscalls.md)
* **Testing Guide:** [docs/development/testing.md](docs/development/testing.md)

### Academic Researcher
Explore HNX architecture and internals:

* **Architecture Overview:** [docs/architecture/overview.md](docs/architecture/overview.md)
* **Memory Management:** [docs/architecture/memory.md](docs/architecture/memory.md)
* **Scheduler Design:** [docs/architecture/scheduler.md](docs/architecture/scheduler.md)
* **Security Model:** [docs/architecture/security.md](docs/architecture/security.md)
* **Hybrid Kernel Analysis:** [docs/research/hybrid-kernels.md](docs/research/hybrid-kernels.md)

### Security Expert
Security documentation and hardening guides:

* **Capability Model:** [docs/security/capabilities.md](docs/security/capabilities.md)
* **Memory Safety:** [docs/security/memory-safety.md](docs/security/memory-safety.md)
* **Isolation:** [docs/security/isolation.md](docs/security/isolation.md)
* **Vulnerability Reporting:** [docs/security/reporting.md](docs/security/reporting.md)

### System Administrator
Configure, tune, and troubleshoot HNX systems:

* **Installation Guide:** [docs/admin/installation.md](docs/admin/installation.md)
* **Configuration:** [docs/admin/configuration.md](docs/admin/configuration.md)
* **Troubleshooting:** [docs/admin/troubleshooting.md](docs/admin/troubleshooting.md)
* **Performance Tuning:** [docs/admin/performance.md](docs/admin/performance.md)

### Service Developer
Build userspace services for HNX:

* **Service Framework:** [docs/services/framework.md](docs/services/framework.md)
* **IPC Development:** [docs/services/ipc.md](docs/services/ipc.md)
* **API Reference:** [docs/services/api.md](docs/services/api.md)
* **Best Practices:** [docs/services/best-practices.md](docs/services/best-practices.md)

### Hardware Vendor
Write drivers and support new hardware:

* **Driver Framework:** [docs/drivers/framework.md](docs/drivers/framework.md)
* **HAL API:** [docs/drivers/hal.md](docs/drivers/hal.md)
* **Device Tree:** [docs/drivers/devicetree.md](docs/drivers/devicetree.md)
* **Submission Process:** [docs/drivers/submission.md](docs/drivers/submission.md)

## Development

### Code Style
- **Rust:** Follow Rust standard formatting (`cargo fmt`)
- **C:** Unix kernel conventions for headers
- **Python:** PEP 8 for configuration scripts
- **Documentation:** All public APIs must have Chinese documentation

### Cross-Architecture Development
HNX is designed as a cross-architecture OS. Follow these conventions:

1. **Conditional Compilation:**
   - Use `#[cfg(target_arch = "aarch64")]` for arch-specific code
   - No hardcoded architecture assumptions in generic code
   - Architecture code in `src/kernel/src/arch/` subdirectories

2. **Architecture Abstraction:**
   - Generic code uses traits and abstract interfaces
   - Memory management, interrupts, context switching have common interfaces
   - Architecture implementations must implement all interface methods

### Documentation Standards
All public APIs require Chinese documentation:
```rust
/// 从 initrd 加载并启动服务
///
/// # Arguments
/// - `path`: Service path in initrd (e.g., `/bin/loader-service`)
///
/// # Returns
/// - `Ok(pid)`: Successfully started service process ID
/// - `Err(error)`: Error code and description
///
/// # Examples
/// ```no_run
/// let pid = spawn_service_from_initrd("/bin/loader-service")?;
/// ```
pub fn spawn_service_from_initrd(path: &str) -> Result<usize, SysError> {
    // Implementation...
}
```

## Communication and Support

### Getting Help
* **GitHub Issues:** Bug reports and feature requests
* **Discussions:** Architecture discussions and design decisions
* **Documentation:** [docs/](docs/) directory for detailed guides
* **Community:** Join our development community

### Reporting Issues
1. Check existing issues and documentation
2. Use issue templates for bugs and features
3. Include system configuration and logs
4. Follow the security reporting process for vulnerabilities

### Contributing
We welcome contributions! See:
* [Contributing Guide](docs/contributing.md)
* [Code Review Process](docs/process/code-review.md)
* [Pull Request Guidelines](docs/process/pull-requests.md)

## Version Information

**Current Version:** 0.2.0-alpha.1  
**Target Architecture:** aarch64  
**Primary Language:** Rust  
**License:** MIT

### Version Management
```bash
# Show current version
make version

# Bump version
make version-bump-major
make version-bump-minor
make version-bump-patch

# Check version consistency
make version-check
```

## License

HNX is released under the MIT License. See individual files for license details.

**Note:** This is an experimental operating system. Not recommended for production use.

---

*"Building the future of operating systems with modern safety guarantees."*

**Primary Language:** Rust  
**Target Architecture:** aarch64  
**Status:** Active Development  
**Last Updated:** January 2024
