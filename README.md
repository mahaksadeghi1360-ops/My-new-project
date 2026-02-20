name: Check Unfollowers
on: [push]

jobs:
  compare:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Comparison
        run: |
          python3 -c "
          import re
          import os

          def extract_usernames(filename):
              if not os.path.exists(filename):
                  return set()
              with open(filename, 'r', encoding='utf-8') as f:
                  content = f.read()
                  return set(re.findall(r'href=\"https://www\.instagram\.com/([^/\"?]+)', content))

          try:
              # اسم فایل‌های شما دقیقا مطابق عکس ۳
              following = extract_usernames('following.html')
              followers = extract_usernames('followers_1.html')
              unfollowers = following - followers

              with open('result.txt', 'w', encoding='utf-8') as f_out:
                  f_out.write('List of people who do not follow you back:\n')
                  f_out.write('------------------------------------------\n')
                  for user in sorted(unfollowers):
                      f_out.write(user + '\n')
              print('Success!')
          except Exception as e:
              print(f'Error: {str(e)}')
          "
      - name: Upload Result
        uses: actions/upload-artifact@v4
        with:
          name: unfollowers-list
          path: result.txt
