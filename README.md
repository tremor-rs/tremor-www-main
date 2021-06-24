# **tremor-www-main**

This site contains the hugo static site generator source and configuration
for the [Tremor Project](https://www.tremor.rs) www site.

## Local testing

When writing new content, the [Hugo](https://gohugo.io/) website framework can
be used to locally test the content and any changes to themes and templates.

### To install hugo on Mac OS X

```bash
brew install hugo
```

### Run the hugo server locally


```bash
hugo server -D
```

The `-D` flag will enable drafts which we disable for the production site but is useful
for work in progress content development

Hugo will watch for changes so it can be kept running during content development with
periodic browser refreshes to see changes. Sometimes a hard refresh may be required.

## License

Content is licensed under ASL 2

The hugo theme is licensed under MIT and is a derived work based on the Kube Hugo theme
