name: Auto Update Reading List
on:
  push:
    paths:
      - 'science/**'
      - 'environment/**'

jobs:
  update-index:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Generate Index
        run: |
          echo "# Science 文章列表" > science/README.md
          echo "| 标题 | 链接 |" >> science/README.md
          echo "| :--- | :--- |" >> science/README.md
          for file in science/*.md; do
            if [ "$file" != "science/README.md" ]; then
              title=$(basename "$file" .md)
              echo "| $title | [阅读原文](./$title.md) |" >> science/README.md
            fi
          done
      - name: Commit changes
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add .
          git commit -m "Auto-update reading list" || exit 0
          git push
