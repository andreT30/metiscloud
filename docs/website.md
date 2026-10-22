# MetisCloud website

The public landing page is [index.html](../index.html). It uses only inline CSS
and the existing [approved demo media](media/README.md). It has no JavaScript,
third-party fonts, analytics, cookies, forms, external embeds or build step.
Its content security policy blocks scripts, network API calls, plugins and forms.
Product documentation links open the rendered guides on GitHub.

## Preview

Open `index.html` in a browser from the repository root. Images and the video
use relative paths, so retain the `docs/media/` directory next to the page.
The video loads when requested through the browser's native controls.
The written product guide is linked beside it; a synchronized caption track is
not currently supplied with the approved recording.

## Publish with GitHub Pages

Website publication and DNS changes are separate from committing the page.

1. Push the approved website commit to the repository's `main` branch.
2. In the GitHub repository's **Settings → Pages**, select **Deploy from a branch**,
   branch **main**, and folder **/ (root)**, then save.
3. Verify ownership of `metiscloud.ai` with GitHub before configuring the domain.
   Follow GitHub's current custom-domain guidance linked below for the required
   verification record and apex-domain DNS records. The supplied [CNAME](../CNAME)
   contains `metiscloud.ai`; use the same custom domain in Pages settings.
4. After GitHub confirms the DNS configuration and provisions a certificate,
   enable **Enforce HTTPS**.
5. Check the live domain on desktop and mobile. Verify navigation, images,
   video playback, documentation links and HTTPS before announcing the site.

See GitHub's [Pages publishing source guide](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site),
[custom-domain guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
and [domain verification guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages).
The `CNAME` file alone does not change DNS or enable GitHub Pages.

## Maintain

Keep product claims aligned with the public guides and preserve the licensing
and demo-data notices. Only add approved public media. Recheck relative asset
paths, documentation anchors, keyboard navigation, narrow screens and video
playback after edits. The page remains usable without JavaScript.
