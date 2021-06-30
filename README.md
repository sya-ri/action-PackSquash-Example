# action-PackSquash-Example

## Upload Artifact On Push [.github/workflows/packsquash.yml](.github/workflows/packsquash.yml)

```yml
name: Optimize ResoucePack
on: [push]
jobs:
  packsquash:
    name: Optimize
    runs-on: ubuntu-latest
    steps:
      - name: Clone Repository
        uses: actions/checkout@master
        with:
          fetch-depth: 1
      - name: Run PackSquash
        uses: sya-ri/action-PackSquash@v1.0.0
        with:
          path: texture
      - name: Output Optimized
        uses: actions/upload-artifact@v2
        with:
          name: optimize-texture
          path: optimize-texture
```

## Create Release On Push Tag [.github/workflows/packsquash-release.yml](.github/workflows/packsquash-release.yml)

```yml
name: Release Optimized ResoucePack
on:
  push:
    tags:
      - '*'
jobs:
  packsquash:
    name: Optimize
    runs-on: ubuntu-latest
    steps:
      - name: Clone Repository
        uses: actions/checkout@master
        with:
          fetch-depth: 1
      - name: Run PackSquash
        uses: sya-ri/action-PackSquash@v1.0.0
        with:
          path: texture
      - name: Copy Output
        uses: canastro/copy-file-action@master
        with:
          source: optimize-texture
          target: optimize-texture.zip
      - name: Get Version
        id: version
        run: echo ::set-output name=VERSION::${GITHUB_REF#refs/tags/}
      - name: Release
        uses: softprops/action-gh-release@v1
        with:
          name: Release ${{ steps.version.outputs.VERSION }}
          files: |
            optimize-texture
            optimize-texture.zip
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
