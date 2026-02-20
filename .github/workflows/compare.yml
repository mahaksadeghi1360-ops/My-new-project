name: Deep Scan
on: [push]
jobs:
  compare:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Scrape Everything
        run: |
          python3 -c "
          import re, os
          def deep_find(fn):
              if not os.path.exists(fn): return set()
              with open(fn, 'r', encoding='utf-8') as f:
                  data = f.read()
              # پیدا کردن هر متنی که شبیه یوزرنیم باشه و بین کدهای استایل نباشه
              found = re.findall(r'href=\"/([^/\"?]+)/\"|d\">([^<]+)</div>', data)
              res = set()
              for m in found:
                  u = m[0] or m[1]
                  if u and 3 < len(u) < 30 and '.' not in u and '{' not in u:
                      res.add(u)
              return res
          
          ing = deep_find('following.html')
          ers = deep_find('followers_1.html')
          unf = sorted(ing - ers)
          
          with open('README.md', 'w', encoding='utf-8') as f:
              f.write('# نتیجه اسکن عمیق\n\n')
              f.write(f'فالووینگ: {len(ing)} | فالوور: {len(ers)}\n\n')
              for u in unf: f.write(f'- {u}\n')
          "
      - name: Push
        run: |
          git config --global user.name 'bot'
          git config --global user.email 'bot@bot.com'
          git add README.md
          git commit -m "deep scan" || exit 0
          git push
