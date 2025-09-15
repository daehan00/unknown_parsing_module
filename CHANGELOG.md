# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.1] - 2025-01-14

### Fixed
- **Critical Syntax Error**: Fixed f-string syntax error in `browser_encoder.py`
  - Resolved unmatched parentheses issue in f-string on line 180
  - Changed `f"{file.get("file_name")}"` to `f"{file.get('file_name')}"` for proper quote nesting
  - Fixed production deployment failure that prevented package import
  - Ensured compatibility with Python f-string syntax requirements

## [0.2.0] - 2025-01-09

### Added
- **Database Integration**: Full PostgreSQL database support with SQLAlchemy ORM
  - `database_data_load()` method for loading forensic data from PostgreSQL database
  - `Config_db` dataclass for database configuration management
  - `Artifact` SQLAlchemy model mapping to `forensic_data_files` table
  - Comprehensive database session management with auto-reconnection
  - Safe session closing with exception handling
- **Enhanced DataLoader**: 
  - Database connection state validation with `_ensure_db_connection()`
  - Automatic session management and cleanup
  - Support for task-based data querying with forensic categories
  - Improved error handling for database operations
- **Comprehensive Test Suite**: 
  - 24 new database integration tests covering all scenarios
  - Mock-based testing for database operations
  - Edge case testing for connection failures and session management
  - 100% test coverage for database functionality
- **Dependencies**: Added SQLAlchemy 2.0+ and psycopg2-binary for PostgreSQL support

### Changed
- **Project Status**: Upgraded from Alpha to Beta (Development Status :: 4 - Beta)
- **Description**: Updated to reflect database integration capabilities
- **Keywords**: Added "database" and "postgresql" to package keywords
- **Python Support**: Added Python 3.13 compatibility
- **Classifiers**: Added "Topic :: Database" classifier

### Fixed
- **SQLAlchemy Deprecation**: Updated from `sqlalchemy.ext.declarative.declarative_base` to `sqlalchemy.orm.declarative_base`
- **Type Safety**: Enhanced type hints with Optional types for database attributes
- **Session Management**: Fixed potential memory leaks with proper session cleanup

### Technical Details
- **New Classes**:
  - `Config_db`: Database configuration dataclass
  - `Artifact`: SQLAlchemy ORM model for forensic data
- **New Methods**:
  - `DataLoader.set_database()`: Initialize database connection
  - `DataLoader.database_data_load()`: Load data from database
  - `DataLoader._ensure_db_connection()`: Validate and maintain connection
  - `DataLoader._close_db_session()`: Safe session cleanup
- **Database Schema Support**:
  - 13 database columns: id, pc_id, task, module_type, collection_time, file_size, checksum, json_data, created_at, update_at, processed, error_message, extracted_info
  - Support for all forensic categories: BROWSER_DATA, DELETED_DATA, USB_DATA, LNK_DATA, MESSENGER_DATA, PREFETCH_DATA
  - JSON data storage and retrieval for complex forensic artifacts

## [0.1.0] - 2025-01-08

### Added
- **Initial Release**: Core forensic data processing functionality
- **Multi-format Support**: Browser, deleted files, link files, messenger, prefetch, USB artifacts
- **Data Sources**: Local file and AWS S3 integration
- **Processing Pipeline**: Standardized data conversion to pandas DataFrames
- **Categories**: Complete forensic data category enumeration
- **Encoders**: Specialized encoders for each forensic data type
- **Cloud Storage**: S3 integration with task-id based structure
- **Logging**: Comprehensive logging system for debugging
- **Type Safety**: Full type hints support
- **Testing**: Comprehensive test suite with S3 integration tests

### Technical Features
- **Core Classes**:
  - `Category`: Enumeration of forensic data types
  - `DataLoader`: Flexible data loading from multiple sources
  - `Encoder`: Factory pattern for data type-specific processing
  - `DataSaver`: Standardized CSV output functionality
- **S3 Integration**:
  - Task-based S3 key structure: `{task_id}/{category}_data.json`
  - AWS profile and region support
  - Automatic credential management
- **Data Processing**:
  - Browser data (Chrome, Edge): History, cookies, downloads, annotations
  - Deleted files: MFT records, recycle bin data
  - Link files: Windows LNK file analysis
  - Messenger data: Chat and communication artifacts
  - Prefetch files: Windows application execution artifacts
  - USB devices: Device connection and usage data
