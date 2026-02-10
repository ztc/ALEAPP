# Technology Stack

## Language
- Python 3.9+ (compatible with older 3.x versions for most modules)

## Core Dependencies
- `sqlite3` - Database parsing (built-in)
- `protobuf` - Protocol buffer parsing
- `blackboxprotobuf` - Unknown protobuf parsing
- `beautifulsoup4` - HTML/XML parsing
- `xmltodict` - XML to dictionary conversion
- `pillow` - Image processing
- `pytz` - Timezone handling

## Forensic/Analysis Libraries
- `simplekml` - KML file generation for geolocation
- `folium` - Interactive map generation
- `geopy` - Geocoding services
- `polyline` - Polyline encoding/decoding
- `fitdecode` - Fitness file parsing

## Security/Crypto
- `bcrypt` - Password hashing
- `PyCryptodome` - Cryptographic operations

## Output/Reporting
- `xlsxwriter` - Excel file generation
- Custom HTML report generation with Bootstrap/MDB

## Build System
- PyInstaller for creating standalone executables

## Common Commands

### Install Dependencies
```bash
pip install -r requirements.txt
# or
pip3 install -r requirements.txt
```

### Linux Additional Requirement
```bash
sudo apt-get install python3-tk
```

### Run CLI
```bash
python aleapp.py -t <zip|tar|fs|gz> -i <input_path> -o <output_path>
```

### Run GUI
```bash
python aleappGUI.py
```

### Build Executables
```bash
# CLI executable
pyinstaller --onefile scripts/pyinstaller/aleapp.spec

# GUI executable
pyinstaller --onefile --noconsole scripts/pyinstaller/aleappGUI.spec
```

### Generate Artifact Path List
```bash
python aleapp.py -p
```

### Create Profile/Case Data
```bash
python aleapp.py -c <output_directory>
```
