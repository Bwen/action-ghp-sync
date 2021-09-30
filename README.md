# bwen/action-ghp-sync
GitHub Action to sync GitHub Pages according to a `source_dir` and possibly a `build_script`.

## ⚠️ WARNING ⚠️
This GitHub Action **will delete everything** in the branch `gh-pages`, and update it with new generated content.

### Example Usage
```yaml
  - uses: Bwen/action-ghp-sync@v1-beta
    with:
      source_dir: project-site/dist
      build_script: bin/gh-pages-build
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
The `source_dir` is mandatory, it can already exist with static content and will get synced on the GitHub Pages. If
that is the case then the `build_script` can be omitted.

The `source_dir` does not have to exist at the repository checkout, if so then the `build_script` needs to create
the directory with content otherwise the GitHub Action will fail.

### 📝 Build Scripts
The script [gh-pages-sync](gh-pages-sync) is also runnable on a local Linux machine without the GitHub Action
ecosystem. Feel free to download it and experiment with it locally on your local GitHub repository. I suggest commenting
out the line `git push -f origin gh-pages` which force pushes the branch `gh-pages`.

If your build script is a bash script, make sure that you have the following lines at the top of it:
```bash
#!/usr/bin/env bash
set -euxo pipefail
```

This will ensure that if anything fails the `Bwen/action-ghp-sync` action is aware of it and does not continue to publish
corrupted content to your GitHub Pages. For more information: http://redsymbol.net/articles/unofficial-bash-strict-mode/

An example of a build script for a Rust workspace repo could be the following:
```bash
#!/usr/bin/env bash
set -euxo pipefail

# Build wasm package for VueJs to include in its build
cd project-wasm
wasm-pack build
cd pkg
npm link

# Build the VueJs site that reference the built wasm
cd ../../project-site
npm install --no-save
npm run build
```

If your build script **is not a bash script**, please make sure that proper exit codes are returned on error
and that your script is executable.
