![GIA](/static/gia-logo.svg)

# GIA Laboratory Developer Documentation

This is the source for https://gialaboratory.github.io


### Generate Reference Documentation

** Reference documentation is now generated automatically by GitHub Actions **

Install graphdoc from https://github.com/2fd/graphdoc

Delete the existing `/report-results/reference` folder.

```
set REPORT_RESULTS_API_KEY=ENTER_API_KEY_HERE
set REPORT_RESULTS_API_ENDPOINT=ENTER_ENDPOINT_HERE
graphdoc -e %REPORT_RESULTS_API_ENDPOINT% -o ./report-results/reference -x "Authorization: %REPORT_RESULTS_API_KEY%"
```



