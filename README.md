<p align=center><img src="https://raw.githubusercontent.com/cncf/artwork/master/projects/tremor/horizontal/color/tremor-horizontal-color.png" width='35%'></p>
<p align=center><a href="https://landscape.cncf.io/selected=tremor">CNCF Early Stage Sandbox Project</p>
<p align=center><a href="https://landscape.cncf.io/category=streaming-messaging&format=card-mode&grouping=category">CNCF Streaming &amp; Messaging</p>

<hr>

[![CII Best Practices]][bestpractices]
[![GitHub]](LICENSE)
[![Discord]][discord-invite]

[CII Best Practices]: https://bestpractices.coreinfrastructure.org/projects/4356/badge
[bestpractices]: https://bestpractices.coreinfrastructure.org/projects/4356
[GitHub]: https://img.shields.io/github/license/tremor-rs/tremor-www-main
[Discord]: https://img.shields.io/discord/752801695066488843.svg?label=&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2
[discord-invite]: https://bit.ly/tremor-discord

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
