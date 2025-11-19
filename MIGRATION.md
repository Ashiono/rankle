# Migration to Modular Architecture

## Overview

Rankle has been refactored from a single monolithic `rankle.py` file to a clean, modular architecture following Python best practices for 2024.

## New Structure

```
rankle/
├── main.py                      # New entry point
├── rankle/                      # Main package
│   ├── __init__.py
│   ├── core/                    # Core functionality
│   │   ├── __init__.py
│   │   ├── scanner.py          # Main RankleScanner class
│   │   └── session.py          # HTTP session management
│   ├── modules/                 # Reconnaissance modules
│   │   ├── __init__.py
│   │   ├── dns.py              # ✅ Implemented
│   │   ├── ssl_tls.py          # TODO
│   │   ├── headers.py          # TODO
│   │   ├── cdn_waf.py          # TODO
│   │   ├── subdomain.py        # TODO
│   │   ├── whois.py            # TODO
│   │   └── geolocation.py      # TODO
│   ├── detectors/               # Technology detectors
│   │   ├── __init__.py
│   │   ├── cms.py              # TODO
│   │   ├── tech.py             # TODO
│   │   └── waf_active.py       # TODO
│   ├── utils/                   # Utilities
│   │   ├── __init__.py
│   │   ├── validators.py       # ✅ Implemented
│   │   └── helpers.py          # ✅ Implemented
│   └── reports/                 # Report generation
│       ├── __init__.py
│       ├── json_report.py      # TODO
│       └── text_report.py      # TODO
├── config/                      # Configuration
│   ├── settings.py             # ✅ Implemented
│   └── tech_signatures.json    # Moved from root
├── tests/                       # Unit tests
│   └── __init__.py
├── docs/                        # Documentation
└── rankle.py                    # Legacy (to be deprecated)
```

## Benefits

### 1. **Modularity**
- Each module has a single responsibility
- Easy to understand, test, and maintain
- Clear separation of concerns

### 2. **Collaboration**
- Multiple developers can work on different modules simultaneously
- Reduced merge conflicts
- Clear ownership of code sections

### 3. **Testability**
- Each module can be tested independently
- Easier to write unit tests
- Better test coverage

### 4. **Extensibility**
- Easy to add new reconnaissance techniques
- Plug-and-play architecture for new detectors
- Simple to add new output formats

### 5. **Clean Code**
- Follows Python PEP 8 and modern best practices
- Type hints for better IDE support
- Clear documentation and docstrings

## Usage

### New Way (Modular)
```bash
# Using the new main.py entry point
python main.py example.com

# With options
python main.py example.com --output json
python main.py example.com --no-save
python main.py example.com --verbose
```

### Old Way (Still works)
```bash
# Legacy rankle.py still works but is deprecated
python rankle.py example.com
```

## Migration Status

### ✅ Completed
- Project structure created
- Configuration system (`config/settings.py`)
- Utilities (`validators.py`, `helpers.py`)
- HTTP session management (`core/session.py`)
- Main scanner orchestrator (`core/scanner.py`)
- DNS analysis module (`modules/dns.py`)
- New entry point (`main.py`)

### 🚧 In Progress
The following modules need to be extracted from `rankle.py`:

1. **HTTP Headers Analysis** (`modules/headers.py`)
   - Status code analysis
   - Server detection
   - Security headers audit

2. **SSL/TLS Certificate Analysis** (`modules/ssl_tls.py`)
   - Certificate information
   - Cipher suites
   - Protocol versions
   - SANs extraction

3. **CDN/WAF Detection** (`modules/cdn_waf.py`)
   - Passive detection via headers
   - Active WAF detection
   - CDN identification

4. **Technology Detection** (`detectors/tech.py`, `detectors/cms.py`)
   - CMS detection
   - Framework identification
   - Library detection
   - Analytics detection

5. **Subdomain Discovery** (`modules/subdomain.py`)
   - Certificate Transparency queries
   - crt.sh integration

6. **WHOIS Lookup** (`modules/whois.py`)
   - Domain registration information
   - Registrar details
   - Expiration dates

7. **Geolocation** (`modules/geolocation.py`)
   - IP geolocation
   - Hosting provider detection
   - Cloud provider identification

8. **Advanced Fingerprinting** (`detectors/fingerprint.py`)
   - HTTP methods testing
   - API endpoint discovery
   - Cookie analysis
   - Error page fingerprinting

9. **Report Generation** (`reports/`)
   - JSON formatter
   - Text formatter
   - HTML formatter (future)

## Configuration

All configuration is now centralized in `config/settings.py`:

```python
from config.settings import (
    DEFAULT_TIMEOUT,
    USER_AGENT,
    DNS_NAMESERVERS,
    # ... etc
)
```

## Development Guidelines

### Adding a New Module

1. Create a new file in the appropriate directory:
   - `rankle/modules/` for reconnaissance modules
   - `rankle/detectors/` for technology detectors
   - `rankle/utils/` for utilities

2. Follow the module template:

```python
"""
Module description
"""
from typing import Dict, Any
from config.settings import RELEVANT_SETTING

class ModuleAnalyzer:
    """Module description"""

    def __init__(self, domain: str):
        self.domain = domain

    def analyze(self) -> Dict[str, Any]:
        """
        Perform analysis

        Returns:
            Dictionary with results
        """
        print("\\n🔍 Module Analysis...")
        # Implementation
        return results
```

3. Add the module to `scanner.py`:

```python
from rankle.modules.your_module import YourAnalyzer

class RankleScanner:
    def __init__(self, ...):
        self._your_analyzer = None

    @property
    def your_analyzer(self):
        if self._your_analyzer is None:
            self._your_analyzer = YourAnalyzer(self.domain)
        return self._your_analyzer

    def analyze_your_feature(self):
        return self.your_analyzer.analyze()
```

4. Update `run_full_scan()` to include your module

5. Write tests in `tests/test_your_module.py`

## Testing

```bash
# Run all tests
pytest tests/

# Run specific module tests
pytest tests/test_dns.py

# With coverage
pytest --cov=rankle tests/
```

## Backward Compatibility

The old `rankle.py` remains functional for backward compatibility but will be deprecated in a future version. Please migrate to the new structure.

## Next Steps

1. ✅ Complete DNS module implementation
2. Implement remaining modules (in order of priority):
   - HTTP Headers
   - SSL/TLS
   - CDN/WAF
   - Technology Detection
3. Add comprehensive unit tests
4. Create integration tests
5. Update documentation
6. Deprecate old `rankle.py`

## Contributing

When contributing to Rankle, please:

1. Follow the modular architecture
2. Add your code to the appropriate module
3. Include docstrings and type hints
4. Write unit tests for your code
5. Update this migration guide

## Questions?

If you have questions about the new structure, please:
- Open an issue on GitHub
- Check the documentation in `/docs`
- Review example modules like `dns.py`

---

**Version**: 2.0.0 (Modular Architecture)
**Date**: November 19, 2025
**Status**: In Progress (Core complete, modules being migrated)
