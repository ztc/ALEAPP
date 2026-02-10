# Project Structure

```
ALEAPP/
├── aleapp.py              # CLI entry point
├── aleappGUI.py           # GUI entry point (Tkinter)
├── requirements.txt       # Python dependencies
├── assets/                # Logo and icon files
└── scripts/
    ├── artifacts/         # Artifact parser plugins (main extension point)
    ├── ccl/               # CCL Forensics helper libraries
    ├── filetypes/         # File type detection modules
    ├── pyinstaller/       # PyInstaller build specs
    ├── _elements/         # HTML report assets (CSS, JS, fonts)
    ├── artifact_report.py # HTML report generation class
    ├── ilapfuncs.py       # Core utility functions
    ├── lavafuncs.py       # LAVA output integration
    ├── plugin_loader.py   # Dynamic plugin loading system
    ├── search_files.py    # File seeking (fs/tar/zip)
    ├── report.py          # Final report assembly
    └── version_info.py    # Version information
```

## Plugin Architecture

Artifact plugins are Python files in `scripts/artifacts/` loaded dynamically at runtime.

### Plugin Definition (v2 - Preferred)
```python
__artifacts_v2__ = {
    "artifact_id": {
        "name": "Display Name",
        "description": "What this artifact extracts",
        "author": "@username",
        "version": "1.0",
        "date": "2024-01-01",
        "requirements": "none",
        "category": "Category Name",
        "notes": "",
        "paths": ('*/path/pattern/*',),
        "function": "function_name"
    }
}
```

### Plugin Definition (v1 - Legacy)
```python
__artifacts__ = {
    "ArtifactName": (
        "Category",
        ('*/search/pattern/*',),
        function_reference
    )
}
```

### Plugin Function Signature
```python
def get_artifact(files_found, report_folder, seeker, wrap_text):
    # files_found: list of matched file paths
    # report_folder: output directory for this category
    # seeker: FileSeekerBase instance for additional file access
    # wrap_text: boolean for text wrapping in output
    pass
```

## Key Modules

- `ilapfuncs.py`: Core utilities - logging, SQLite helpers, TSV/timeline/KML output, media handling
- `artifact_report.py`: `ArtifactHtmlReport` class for HTML report generation
- `search_files.py`: `FileSeekerDir`, `FileSeekerTar`, `FileSeekerZip` for file extraction
- `plugin_loader.py`: `PluginLoader` class for dynamic artifact module loading

## Output Structure
Reports are generated in `ALEAPP_Reports_<timestamp>/`:
- `_HTML/` - HTML reports by category
- `_TSV Exports/` - Tab-separated data files
- `_Timeline/` - Timeline database
- `_KML Exports/` - Geolocation KML files
- `data/` - Extracted source files
- `Script Logs/` - Processing logs
