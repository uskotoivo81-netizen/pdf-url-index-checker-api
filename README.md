# PDF URL Index Checker API: check PDF indexing status step by step

This repository is a short working guide for anyone who needs a **PDF URL index checker API** workflow: first confirm that a PDF can be indexed at all, then check the indexing status for a whole list of PDF URLs and, if needed, automate that check.

The guide is part of the weekly PDF indexing series on [SpeedyIndex](https://uskotoivo81-netizen.github.io/fix-pdf-indexing-problems-in-google/).

## What you need before checking indexing status

A PDF can be perfectly readable for people and still be a poor candidate for a search index. Check these four things first:

1. **HTTP response.** Request the PDF URL directly, not the download landing page:

   ```bash
   curl -sI -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://example.com/files/report.pdf \
     | grep -i -E '^(HTTP/|x-robots-tag:|link:|content-type:|location:)'
   ```

   You want a `200` status and `content-type: application/pdf`. A redirect to a login page, a `403` for the bot or `text/html` means the crawler does not receive the document.

2. **Indexing directives.** A PDF has no `<head>`, so a robots meta tag cannot be used. The directive travels in the `X-Robots-Tag` response header. If the header says `noindex`, the file is closed on purpose.

3. **robots.txt.** If the URL is disallowed, the crawler never downloads the file and never reads the header. Check the rule for the exact path before you debug anything else.

4. **Text layer.** Select a paragraph in the PDF and paste it into a text editor. If nothing sensible appears, the file is a scan, and search engines handle scans differently.

## Check indexing status for a list of PDF URLs

Once the four checks above pass, collect the direct PDF URLs in a plain list and check which of them are already in the index:

- [check a list of PDF URLs for indexing status](https://en.speedyindex.com/google-index-checker/) with the SpeedyIndex index checker;
- for scripts, CMS plugins and bulk processing, see the [SpeedyIndex API documentation](https://en.speedyindex.com/api.php).

A status report tells you what is known right now. It does not change a `noindex` header, an empty text layer or a blocked path, and it does not set a date by which a file will appear in search.

## Notes by search engine

- **Google** lists PDF among the file types it can index. A canonical can be sent for a PDF in an HTTP `Link` header, and the choice of the canonical URL stays a signal rather than a command.
- **Yandex** documents a size limit of 10 MB per file; a PDF made only of images is processed only for its first three pages, while a PDF with text is indexed in full.

## FAQ

**Why is my PDF not indexed although it opens in the browser?**
A browser is not a crawler. Check the response the bot receives: status code, `X-Robots-Tag`, redirects and the robots.txt rule for the path.

**Can I use a robots meta tag for a PDF?**
No. A PDF has no HTML head. Use the `X-Robots-Tag` header on the server.

**Is the Google Indexing API a way to submit ordinary PDFs?**
No. Google documents it for job postings and livestream pages only.

**Should I check status before or after fixing headers?**
After. A status check on a closed or empty file only repeats the problem.

## Sources

- Google Search Central: [File types indexable by Google](https://developers.google.com/search/docs/crawling-indexing/indexable-file-types)
- Google Search Central: [Robots meta tag and X-Robots-Tag specifications](https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag)
- Yandex Webmaster Help: [How documents are indexed](https://yandex.ru/support/webmaster/ru/robot-workings/documents-indexing)
