# Implementation Plan: FileSeekerWeb

## Overview

This plan implements a minimal REST API-based file seeker for ALEAPP. The implementation is broken into incremental steps: core client class, reference server, CLI integration, and documentation.

## Tasks

- [ ] 1. Implement FileSeekerWeb core class
  - [ ] 1.1 Add FileSeekerWeb class to scripts/search_files.py
    - Inherit from FileSeekerBase
    - Implement `__init__` with base_url and data_folder parameters
    - Initialize requests.Session for connection reuse
    - Add URL validation (must be http:// or https://)
    - Initialize caching dictionaries (searched, copied, file_infos)
    - _Requirements: 2.1, 2.2, 2.4_

  - [ ] 1.2 Implement search() method
    - Check cache first (unless force=True)
    - Call POST /files/list endpoint with pattern
    - Parse JSON response into file list
    - Download each matched file (respecting copied cache)
    - Return list of local paths (or single path if return_on_first_hit)
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_

  - [ ] 1.3 Implement file download logic
    - Call GET /files/download endpoint with path parameter
    - Stream response to local file (follow redirects automatically)
    - Preserve directory structure in data_folder
    - Create FileInfo with source_path and mtime
    - Handle download errors gracefully (log and continue)
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6_

  - [ ] 1.4 Implement cleanup() method
    - Close requests.Session
    - Make idempotent (safe to call multiple times)
    - _Requirements: 6.1, 6.2_

  - [ ]* 1.5 Write property test for pattern matching
    - **Property 1: Pattern Matching Returns Correct Files**
    - **Validates: Requirements 3.1**

  - [ ]* 1.6 Write property test for download integrity
    - **Property 2: Download Content Integrity (Round-Trip)**
    - **Validates: Requirements 1.3, 4.1**

  - [ ]* 1.7 Write property test for caching behavior
    - **Property 6: Search Result Caching**
    - **Property 8: Download Caching**
    - **Validates: Requirements 3.4, 4.3**

- [ ] 2. Checkpoint - Core class complete
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 3. Implement reference FastAPI server
  - [ ] 3.1 Create scripts/web_api_server.py
    - Set up FastAPI app with configurable EXTRACTION_ROOT
    - Add startup instructions in docstring
    - _Requirements: 5.1, 5.2_

  - [ ] 3.2 Implement POST /files/list endpoint
    - Accept JSON body with pattern field
    - Walk EXTRACTION_ROOT directory tree
    - Filter files matching glob pattern
    - Return JSON with path, size, mtime for each match
    - _Requirements: 1.2_

  - [ ] 3.3 Implement GET /files/download endpoint
    - Accept path query parameter
    - Validate path is within EXTRACTION_ROOT (security)
    - Return StreamingResponse for file content
    - Support redirect mode via optional config
    - _Requirements: 1.3, 5.3_

  - [ ]* 3.4 Write unit tests for reference server
    - Test list endpoint with various patterns
    - Test download endpoint with valid/invalid paths
    - Test path traversal protection
    - _Requirements: 5.1, 5.2, 5.3_

- [ ] 4. Integrate with ALEAPP CLI
  - [ ] 4.1 Update aleapp.py argument parser
    - Add 'web' to -t choices
    - Update help text to describe web source usage
    - _Requirements: 7.1_

  - [ ] 4.2 Add FileSeekerWeb instantiation in crunch_artifacts
    - Add elif branch for extracttype == 'web'
    - Instantiate FileSeekerWeb with input_path as base_url
    - _Requirements: 7.2, 7.3_

  - [ ]* 4.3 Write integration test for CLI with web source
    - Test end-to-end with mock server
    - Verify artifacts processed correctly
    - _Requirements: 7.3_

- [ ] 5. Checkpoint - Integration complete
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 6. Create documentation
  - [ ] 6.1 Create WEB_API_README.md
    - Document API contract (2 endpoints)
    - Include request/response examples
    - Explain redirect support for cloud storage
    - Provide curl examples for testing
    - _Requirements: 1.5_

  - [ ] 6.2 Add usage examples to WEB_API_README.md
    - Example: Running reference server
    - Example: Using ALEAPP with web source
    - Example: Adapting to custom backend
    - _Requirements: 5.4, 5.5_

- [ ] 7. Add remaining property tests
  - [ ]* 7.1 Write property test for invalid URL rejection
    - **Property 3: Invalid URL Rejection**
    - **Validates: Requirements 2.2**

  - [ ]* 7.2 Write property test for return_on_first_hit
    - **Property 5: Return On First Hit Behavior**
    - **Validates: Requirements 3.2**

  - [ ]* 7.3 Write property test for force flag behavior
    - **Property 7: Search Force Bypasses Cache**
    - **Property 9: Download Force Bypasses Cache**
    - **Validates: Requirements 3.5, 4.4**

  - [ ]* 7.4 Write property test for directory structure
    - **Property 10: Directory Structure Preservation**
    - **Validates: Requirements 4.2**

  - [ ]* 7.5 Write property test for cleanup idempotence
    - **Property 12: Cleanup Idempotence**
    - **Validates: Requirements 6.2**

- [ ] 8. Final checkpoint
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- The reference server is intentionally simple for easy adaptation
- Property tests use `hypothesis` library with minimum 100 iterations
- The `requests` library handles redirects automatically, enabling cloud storage integration
