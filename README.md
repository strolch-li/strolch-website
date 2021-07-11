# Strolch website

## GoHugo

This website is created with the static site generator [Hugo](https://gohugo.io/).

### Theme

Uses the [Hugo-theme-learn](https://learn.netlify.app/en/).

Special layout components are explained on [learn.netlify.app/en/shortcodes](https://learn.netlify.app/en/shortcodes/notice/).

### Additional tools 

#### Gallery image view

https://www.liwen.id.au/heg/#gallery-usage
https://github.com/liwenyip/hugo-easy-gallery/

Example use:

```
{{< gallery >}}
{{< figure link="/assets/... .png" caption="" caption-position="center" caption-effect="fade" >}}
{{< figure link="/assets/... .png" caption="" caption-position="center" caption-effect="fade" >}}
{{< figure link="/assets/... .png" caption="" caption-position="center" caption-effect="fade" >}}
{{< /gallery >}}
{{< load-photoswipe >}}
```

The last line only must be added once per page if multiple galleries are used.

### Text format

All pages are separate md-files inside the [content](content/) directory. The formatting
of the text needs to use the rules specified in [commonmark.org](https://spec.commonmark.org/0.29/).

### Test locally

To test the site locally, first install Hugo as described on ["Install Hugo"](https://gohugo.io/getting-started/installing/).

Example, for Mac:

```
brew install hugo
```

To run the site, open the terminal in the directory with the sources of this site and run the following command:

```
cd strolch.li
hugo serve
```

The website is now available on [localhost:1313](http://localhost:1313/).

### Build and run on GitHub Pages

Using a GitHub Action, the site is published on each commit into the main branch of this repository.

***The "docs" directory is auto-generated by this GitHub Action so should never be manually touched. This directory is pushed to GitHub Pages.***

This process is described here:

* [Automate your GitHub Pages Deployment using Hugo and Actions](https://medium.com/@asishrs/automate-your-github-pages-deployment-using-hugo-and-actions-518b959a51f9)
* [Deploy Hugo project to GitHub Pages with GitHub Actions](https://discourse.gohugo.io/t/deploy-hugo-project-to-github-pages-with-github-actions/20725)
