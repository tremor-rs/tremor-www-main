+++
title = "Quick Developer Install"
date = "2020-02-04T14:01:00+01:00"
draft = false
weight = 100
description = "Install"
bref= "Notes about tremor installation for developers"
toc= true
+++

<h3 class="section-head" id="h-platforms"><a href="#h-platforms"></a>Supported Platforms</h3>

Select the operating system your are developing on.

<nav class="tabs" data-component="tabs">
  <ul>
    <li class="active">
      <a href="#os-macosx">Mac OS X</a>
    </li>
    <li>
      <a href="#os-linux">Linux</a>
    </li>
    <li>
      <a href="#os-windows">Windows</a>
    </li>
  </ul>
</nav>

<div id="os-macosx">
  <table class="bordered striped">
    <tr><th>Type</th><th>Is Supported?</th></tr>
    <tr><td>IDE Support</td><td>Yes. <a href="https://macvim-dev.github.io/macvim/">Macvim</a> or <a href="https://code.visualstudio.com">Visual Studio Code</a></td></tr>
    <tr><td>Development</td><td>Yes</td></tr>
    <tr><td class="w20">Production</td><td>No</td></tr>
  </table>
</div>

<div id="os-linux">
  <table class="bordered striped">
    <tr><td>IDE Support</td><td>Yes. Vim ( out of the box ) or <a href="https://code.visualstudio.com">Visual Studio Code</a></td></tr>
    <tr><th>Type</th><th>Is Supported?</th></tr>
    <tr><td>Development</td><td>Yes</td></tr>
    <tr><td class="w20">Production</td><td>Yes</td></tr>
  </table>
</div>

<div id="os-windows">
  <table class="bordered striped">
    <tr><th>Type</th><th>Is Supported?</th></tr>
    <tr><td>IDE Support</td><td>Yes</td></tr>
    <tr><td>Development</td><td>Accepting contributions</td></tr>
    <tr><td class="w20">Production</td><td>Accepting contributions</td></tr>
  </table>
</div>


> ```bash
> git clone https://github.com/wayfair-incubator/tremor-runtime.git
> ```

<h3 class="section-head" id="h-ide"><a href="#h-ide"></a>Setup an IDE / editor</h3>
<nav class="tabs" data-component="tabs">
  <ul>
    <li class="active">
      <a href="#ide-vim">VIM</a>
    </li>
    <li>
      <a href="#ide-vscode">Visual Studio Code</a>
    </li>
    <li>
      <a href="#ide-other">Other</a>
    </li>
  </ul>
</nav>


<div id="ide-vim">
  Follow the instructions in the <a href="https://github.com/wayfair-incubator/tremor-vim">tremor-vim</a> git repository, and
  ensure your `.vimrc` is updated and that you have the <a href="https://github.com/dense-analysis/ale">vim ALE</a> asynchronous
  lint engine.


  > ```bash
  > cd $HOME/.vim/bundle
  > git clone https://github.com/wayfair-tremor/tremor-vim.git
  > ```

</div>

<div id="ide-vscode">
  Follow the instructions in the <a href="https://github.com/wayfair-incubator/tremor-vscode">tremor-vscode</a> git repository.
</div>

<div id="ide-other">
  We are accepting contributions to support other IDEs.
</div>

<h3 class="section-head" id="h-trill"><a href="#h-trill"></a>Setup the tremor language server</h3>

  Clone the tremor-langauge-server git repository, build and install the server and place the binary on your path

  > ```bash
  > cd $HOME/git
  > git clone https://github.com/wayfair-incubator/tremor-language-server.git
  > cd tremor-language-server
  > cargo build --release
  > export PATH=`pwd`/target/release/tremor-language-server:$PATH
  > ```

<h3 class="section-head" id="h-runtime"><a href="#h-runtime"></a>Setup the tremor runtime</h3>

  > ```bash
  > cd $HOME/git
  > git clone https://github.com/wayfair-incubator/tremor-runtime.git
  > cd tremor-runtime
  > cargo build --release # go get a nice cup of tea
  > ```

