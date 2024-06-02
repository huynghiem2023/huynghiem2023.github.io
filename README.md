# 1. Create Content and Table of Contents (TOC)

- Create Content
  - create parent folder (e.g. 'src/personal')
  - create a child folder (e.g. 'src/personal/life')
  - create a child title page (e.g. 'src/personal/life.md')
  - create a content notebooks for that folder (e.g. 'src/personal/life/notebooks/lesson_1.md')
  - create content for lesson 1 (e.g. embed videos from YouTube like section #3.2)
  - create content for an image (![...](./images/***.png))
- Update Table of Contents
  - go to _toc.yml
  - copy the code for the structure similar to existing structure
- Deploy
  - Refer to section #2
  
# 2. Deploy to GitHub Pages

```sh
(jb clean src) -and (jb build src) -and (ghp-import -c huynghiem.com -n -p -f src/_build/html)
```

```PowerShell
(jb clean src) -and (jb build src) -and (ghp-import -n -p -f src/_build/html)
```
# 3. YouTube Videos

## 3.1. Link to YouTube

[[Alt](https://markdown-videos-api.jorgenkh.no/youtube/daqfr6DJsGc)](<https://www.youtube.com/watch?v=daqfr6DJsGc&list=RDMMQJCpDCHqDuU&index=5>)

## 3.2. Embed YouTube Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/daqfr6DJsGc?si=RtYKvdSN1Ezv37oZ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
