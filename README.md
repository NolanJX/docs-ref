# docs-ref

Collects third-party docs for LLM reference.
Use it to provide focused context to coding agents and retrieval workflows.

## Third-Party Notices

Docs are organized by source and preserve upstream structure.
Source and license are tracked in `THIRD_PARTY_NOTICES.md`.

## How to extract LangChain reference docs

```bash
git clone --branch main --single-branch --depth 1 https://github.com/langchain-ai/docs.git langchain-docs

SRC=langchain-docs/src
DEST=LangChain
DIR_EXC=(--exclude='releases/' --exclude='migrate/' --exclude='contributing/' --exclude='images/' --exclude='mermaid/' --exclude='example_data/')

rsync -am "${DIR_EXC[@]}" "$SRC/oss/langchain/"  "$DEST/langchain/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/langgraph/"  "$DEST/langgraph/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/deepagents/" "$DEST/deepagents/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/javascript/" "$DEST/javascript/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/python/"     "$DEST/python/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/concepts/"   "$DEST/concepts/"
rsync -am "${DIR_EXC[@]}" "$SRC/oss/reference/"  "$DEST/reference/"
rsync -am "${DIR_EXC[@]}" "$SRC/langsmith/"      "$DEST/langsmith/"
```

