# Processing VimWiki with Pandoc

Pandoc can turn a VimWiki into HTML.  These instructions expect the VimWiki path to be ~/blog, with generated HTML in ~/html.

```bash
cd ~/blog
find wiki -iname "*.md" -type f -exec sh -c 'pandoc -s "${0}" --template "${1}/blog/template.html" -f markdown -t html5 -o "${1}html/$(basename ${0%.md}.html)"' {} ~ \;
```

