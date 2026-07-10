# pdx-tag

Bidirectional converter between PDX .gui files and a tagged block format.

Tagged format replaces multi-line block braces with named markers:
    Block_open:name  replaces {
    Block_close:name replaces }

Single-line balanced braces (e.g., `size = { 100 50 }`) are left as-is.
Braces inside comments (`# }`) and string literals (`"}"`) are never
treated as structure; `#` inside a string does not start a comment.

Workspace: .gui_workspace/ at mod root (auto-detected via .metadata/).

Usage:
    pdx-tag open  <file.gui>        Tag and copy to workspace (round-trip verified)
    pdx-tag save  <file> [--force]  Untag workspace file back to mod source
                                    (refuses if brace balance would change)
    pdx-tag list                    Show files currently in workspace
    pdx-tag diff  <file.gui>        Diff workspace vs mod source
    pdx-tag check <file.gui>        Verify tag/untag round-trip + brace balance
    pdx-tag tag   <file> --stdout   Tag to stdout (no workspace)
    pdx-tag untag <file> --stdout   Untag to stdout (no workspace)

## Install

Self-contained Python 3.9+ script, no dependencies. Symlink or copy it onto your PATH:

```bash
ln -s "$(pwd)/pdx-tag" ~/.local/bin/pdx-tag
```
