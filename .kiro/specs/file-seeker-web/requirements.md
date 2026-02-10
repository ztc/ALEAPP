# Requirements Document

## Introduction

This document defines the requirements for a new `FileSeekerWeb` implementation that enables ALEAPP to process Android forensic artifacts from a simple REST API. The design prioritizes minimal implementation effort with a small API surface, making it easy for developers to understand and adapt to their own backend storage systems (SQLite, PostgreSQL, cloud storage, etc.).

The deliverables include:
- `FileSeekerWeb` class implementing the `FileSeekerBase` interface
- `WEB_API_README.md` documenting the minimal REST API contract
- A reference FastAPI server implementation demonstrating the API with a local directory

## Glossary

- **File_Seeker**: A component that implements the `FileSeekerBase` interface to locate and retrieve files matching specified patterns for artifact parsing
- **Web_API**: A minimal REST API that provides file listing and file download endpoints
- **API_Server**: A backend server implementing the Web_API contract (reference implementation uses FastAPI)
- **Data_Folder**: The local directory where retrieved files are cached for artifact processing
- **File_Pattern**: A glob-style pattern (e.g., `*/data/com.app/*`) used to match files within an extraction
- **File_Info**: Metadata about a file including source path, creation date, and modification date

## Requirements

### Requirement 1: Minimal REST API Contract

**User Story:** As a developer, I want a minimal REST API specification, so that I can quickly implement a backend that serves forensic extractions to ALEAPP.

#### Acceptance Criteria

1. THE Web_API SHALL require only two endpoints: one for listing files and one for downloading files
2. THE listing endpoint SHALL accept a glob pattern and return matching file paths with metadata
3. THE download endpoint SHALL accept a file path and return the file content as a stream
4. THE Web_API SHALL use JSON for request/response bodies (except file downloads)
5. THE Web_API specification SHALL be documented in WEB_API_README.md with examples

### Requirement 2: File Seeker Initialization

**User Story:** As a forensic investigator, I want to initialize a file seeker with a base URL, so that I can process Android extractions from any server implementing the API.

#### Acceptance Criteria

1. WHEN a user provides a valid HTTP or HTTPS base URL, THE File_Seeker SHALL initialize with that endpoint
2. WHEN a user provides an invalid URL format, THE File_Seeker SHALL raise a descriptive error
3. WHEN the API server is unreachable, THE File_Seeker SHALL raise a connection error with details
4. THE File_Seeker SHALL store the base URL and data folder for subsequent operations

### Requirement 3: File Pattern Search

**User Story:** As a forensic investigator, I want to search for files matching glob patterns, so that artifact parsers can locate relevant evidence files.

#### Acceptance Criteria

1. WHEN a file pattern is provided, THE File_Seeker SHALL call the listing endpoint and return matching file paths
2. WHEN `return_on_first_hit` is True, THE File_Seeker SHALL return only the first matching file path
3. WHEN no files match the pattern, THE File_Seeker SHALL return an empty list
4. THE File_Seeker SHALL cache search results to avoid redundant API calls
5. WHEN `force` is True, THE File_Seeker SHALL bypass the cache and make a fresh API call

### Requirement 4: File Download and Caching

**User Story:** As a forensic investigator, I want matched files to be downloaded to a local data folder, so that artifact parsers can process them locally.

#### Acceptance Criteria

1. WHEN a file matches a search pattern, THE File_Seeker SHALL download it via the download endpoint
2. THE File_Seeker SHALL preserve the original directory structure when saving downloaded files
3. THE File_Seeker SHALL track downloaded files to avoid re-downloading previously retrieved files
4. WHEN `force` is True, THE File_Seeker SHALL re-download files even if previously cached
5. IF a download fails, THEN THE File_Seeker SHALL log the error and continue processing other files
6. THE File_Seeker SHALL create File_Info objects with source path and available timestamp metadata

### Requirement 5: Reference FastAPI Server

**User Story:** As a developer, I want a working reference server implementation, so that I can understand the API contract and adapt it to my storage backend.

#### Acceptance Criteria

1. THE reference server SHALL implement both required API endpoints using FastAPI
2. THE reference server SHALL serve files from a local directory structure
3. THE reference server SHALL demonstrate streaming file downloads for large files
4. THE reference server code SHALL be simple and well-commented for easy adaptation
5. THE reference server SHALL include example usage instructions

### Requirement 6: Resource Cleanup

**User Story:** As a forensic investigator, I want proper cleanup of network resources, so that connections are properly closed after processing.

#### Acceptance Criteria

1. WHEN cleanup is called, THE File_Seeker SHALL close any open HTTP sessions
2. THE File_Seeker SHALL be safe to call cleanup multiple times without error

### Requirement 7: CLI Integration

**User Story:** As a forensic investigator, I want to specify web sources via command line, so that I can process remote extractions using the existing ALEAPP workflow.

#### Acceptance Criteria

1. THE CLI SHALL accept a new extract type 'web' for web-based sources
2. WHEN extract type is 'web', THE CLI SHALL accept a base URL as the input path
3. THE CLI SHALL instantiate FileSeekerWeb and process artifacts normally

### Requirement 8: Error Handling

**User Story:** As a forensic investigator, I want clear error messages, so that I can diagnose connection or API issues.

#### Acceptance Criteria

1. WHEN an API call fails, THE File_Seeker SHALL log a descriptive error message
2. WHEN a download fails, THE File_Seeker SHALL continue processing other files
3. THE File_Seeker SHALL handle HTTP error status codes appropriately
