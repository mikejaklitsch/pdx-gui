# pdx-tag

Bidirectional converter between Paradox `.gui` files and a tagged block format. Large `.gui` files are hard to edit safely because one misplaced brace corrupts everything after it, and braces also appear inside comments and string literals where they are not structure. pdx-tag replaces every multi-line block brace with a named marker pair, so blocks can be read, extracted, and edited by name instead of by brace counting, then converts the file back losslessly.

The tagged format:

```
window = {          becomes        window = Block_open:window
    ...                                ...
}                                  Block_close:window
```

Single-line balanced braces like `size = { 100 50 }` are left alone. The scanner is comment-aware and string-aware: `# }` in a comment and `"}"` in a string are never treated as structure, and `#` inside a string does not start a comment.

## Install

Self-contained Python script, no dependencies. Symlink it onto your PATH:

```bash
ln -s "$(pwd)/pdx-tag" ~/.local/bin/pdx-tag
```

## Usage

The workspace is a `.gui_workspace/` directory at the mod root, found automatically via the mod's `.metadata/` directory. Run commands from anywhere inside the mod.

```bash
pdx-tag open  file.gui           # tag the file and copy it into the workspace
pdx-tag save  file.gui           # untag the workspace copy back over the mod source
pdx-tag save  file.gui --force   # save even if the net brace balance changed
pdx-tag list                     # show files currently open in the workspace
pdx-tag diff  file.gui           # diff workspace copy against the mod source
pdx-tag check file.gui           # report round-trip fidelity and brace balance
pdx-tag tag   file.gui --stdout  # tag to stdout, no workspace
pdx-tag untag file.tagged --stdout
```

The editing loop is: `open`, edit the `.tagged` file in the workspace, `save`. The mod's `.gui` files stay canonical; the workspace is scratch space and is cleaned up on save.

## Safety guarantees

- `open` verifies the tag/untag round-trip reproduces the original file byte for byte before giving you a workspace copy. Files that cannot round-trip are refused.
- `save` refuses if untagging would change the file's net brace balance, which means marker pairs were broken during editing. Fix the workspace copy or pass `--force`.
- `save` also refuses if `Block_` marker text would survive outside comments in the output.

## Comparing files structurally

Tag both sides to stdout and diff the tagged output. Block boundaries are the marker pairs, so extraction between them is exact, with no brace arithmetic. Format both sides identically first if one side is hand-formatted, or the diff will drown in layout noise.
