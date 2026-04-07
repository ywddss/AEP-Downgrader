# macOS Build Instructions

## Prerequisites
- Python 3.7+
- pip
- Xcode command line tools

## Building

1. Create a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

2. Install dependencies:
   ```bash
   pip install pyinstaller PyQt5 psutil
   ```

3. Generate `.icns` icon from `assets/icon.png`:
   ```bash
   rm -rf assets/icon.iconset
   mkdir -p assets/icon.iconset
   sips -z 16 16     assets/icon.png --out assets/icon.iconset/icon_16x16.png
   sips -z 32 32     assets/icon.png --out assets/icon.iconset/icon_16x16@2x.png
   sips -z 32 32     assets/icon.png --out assets/icon.iconset/icon_32x32.png
   sips -z 64 64     assets/icon.png --out assets/icon.iconset/icon_32x32@2x.png
   sips -z 128 128   assets/icon.png --out assets/icon.iconset/icon_128x128.png
   sips -z 256 256   assets/icon.png --out assets/icon.iconset/icon_128x128@2x.png
   sips -z 256 256   assets/icon.png --out assets/icon.iconset/icon_256x256.png
   sips -z 512 512   assets/icon.png --out assets/icon.iconset/icon_256x256@2x.png
   sips -z 512 512   assets/icon.png --out assets/icon.iconset/icon_512x512.png
   sips -z 1024 1024 assets/icon.png --out assets/icon.iconset/icon_512x512@2x.png
   iconutil -c icns assets/icon.iconset -o assets/icon.icns
   ```

4. Build the application bundle:
   ```bash
   pyinstaller src/AEPdowngrader.py --onedir --windowed --noconsole --name AEP-Downgrader --icon=assets/icon.icns --osx-bundle-identifier com.itsanchorpoint.aepdowngrader --add-data "assets:assets" --hidden-import=psutil --hidden-import=debug_logger --collect-all=PyQt5 --collect-all=debug_logger
   ```

5. Create distribution package:
   ```bash
   # The application bundle will be in dist/AEP-Downgrader.app
   # Create DMG for distribution with Applications shortcut
   rm -rf release-macos
   mkdir -p release-macos
   cp -R dist/AEP-Downgrader.app release-macos/
   ln -s /Applications release-macos/Applications
   hdiutil create -volname "AEP-Downgrader" -srcfolder release-macos -ov -format UDZO AEP-Downgrader-macOS.dmg
   ```

## Alternative: One-file executable
```bash
pyinstaller src/AEPdowngrader.py --onefile --windowed --name AEP-Downgrader --add-data "assets:assets" --hidden-import=psutil --hidden-import=debug_logger --collect-all=PyQt5 --collect-all=debug_logger
```

## Result
This creates a macOS application bundle that can be distributed as a DMG file for easy installation to the Applications folder.
