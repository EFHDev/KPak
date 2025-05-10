# KPak

<p align="center"><img src="/assets/KPak.png" style="height: 200px;"></p>

---

KPak is a simple Go library for bundling assets into a single compressed archive. It’s designed for game development and modding, offering features like metadata, dependency tracking, and versioning.

```
Warning! KPak is in early devolopment! It is intended be good enough to replace Unity Asset bundles or Godot Paks and be simpler,
but IT WILL TAKE TIME TO GET THERE!
```


## Features

- **Bundling**: Pack multiple files into one archive.
- **Compression**: Uses zstd for fast and efficient compression.
- **Metadata**: Stores details like file type, size, and checksum.
- **Dependencies**: Automatically handles dependencies for GDScript and JSON files.
- **Versioning**: Supports different bundle versions.
- **Filtering**: Include or exclude files with patterns.
- **Extraction**: Unpack individual files or the entire bundle.

## Planned

- **Scan for networking code**
- **Reject assets that interact with networking packages or other packages users/modders should not need**
- **And you know, a bunch more stuff like those two above..**

## Usage


### Supported file-types

- Texture: `.png, .jpg, .jpeg`
- Audio: `.mp3, .wav, .ogg`
- GDScript: `.gd`
- Scene: `.tscn`
- JSON: `.json`
- Protobuf: `.proto, Binary Proto`
- Model: `.obj, .glb, .gltf`
- Shader: `.shader, .gdshader`
- Font: `.ttf, .otf`
- Material: `.material`
- Animation: `.anim`
- Binary/Unknown: `Anything`

### Creating a Bundle

To bundle assets from a directory:

```go
err := kpak.CreateBundle(
    "path/to/assets",    // Asset directory
    "my_bundle",         // Output file name (no extension)
    "YourName",          // Creator name (required)
    &kpak.BundleOptions{ // Optional settings
        Compression: zstd.SpeedBestCompression,
        Description: "My asset bundle",
        IncludePatterns: []string{"*.png", "*.gd"},
        ExcludePatterns: []string{"*.tmp"},
    },
)
if err != nil {
    log.Fatal(err)
}
```


### Extracting Assets
To open and extract from a bundle:

```go
bundle, err := kpak.OpenBundle("my_bundle.kbp")
if err != nil {
    log.Fatal(err)
}
defer bundle.Close()

// List all assets
assets := bundle.ListAssets()
for _, asset := range assets {
    fmt.Println(asset.Path)
}

// Extract one asset
err = bundle.ExtractAsset("textures/sprite.png", "output/sprite.png")
if err != nil {
    log.Fatal(err)
}

// Extract everything
err = bundle.ExtractAll("output_folder")
if err != nil {
    log.Fatal(err)
}
```

# Notes

**Note**: The `Creator` field is required. Bundles without it won’t work. This is by design. We recommend you lock this down and use it to ban malicious users/modders.

**Note**: We also recommend you lock down how modders/users can interact with outside networks. Eventually we will add a feature to scan code for specific packages or imports, but until then you must do this yourself. THERE IS NO REMOTE EXEUCTION PROTECTION AS OF May 10th, 2025

## Bundle Format

A KPak bundle (`.kbp` file) includes:

- **Magic Number**: "KPAK" to identify the file.
- **Version**: Tracks the bundle format version.
- **Header**: JSON with info like creator, asset count, and creation time.
- **Assets**: Compressed files with metadata (path, type, size, etc.).
- **Metadata Offset**: Quick access to asset details.

## Contributing
Want to help? Submit issues or pull requests to the [GitHub repository](https://github.com/EFHDev/KPak).

## License

Licensed under the [MIT License](LICENSE).

---
