# iphone-safari-check

Opens a web page in Safari on an iPhone simulator and saves a screenshot. It runs on GitHub's Macs, so you need neither an iPhone nor a Mac.

There is no Appium or Xcode project to set up: just one workflow file, [`check.yml`](.github/workflows/check.yml), and a folder of web files, `page/`.

## Use it

1. Press **Use this template**, then **Create a new repository**, and make it **public** (see [Cost](#cost)).
2. Replace what is in `page/` with your own files. The page it opens is `page/index.html`.
3. Open the **Actions** tab, choose **Check**, then **Run workflow**.
4. Wait about 20 minutes, open the finished run and download a `result-…` file. `screen.png` inside it is the screenshot.

The example page compares two code fonts, so you can run it once before changing anything.

To add it to a repository you already have, copy `check.yml` into `.github/workflows/` and change `--directory page` to the folder your page is in. If your site needs a build, add the build steps before "Serve the page on this Mac".

## What you get

Each run uses three Macs at once. These are the versions on 25 November 2026, and GitHub updates them.

| Mac | Phone | iOS |
|---|---|---|
| `macos-15` | iPhone 16 | 26.2 |
| `macos-26` | iPhone 17 | 26.5 |
| `xcode-27` | iPhone 17 | 27.2 |

To use fewer, delete lines from `matrix` in `check.yml`.

- The screenshot shows the first screen of the page, not the full length.
- Safari sometimes shows a tip that covers the bottom of the screen.
- It cannot tap, scroll or rotate.

## Cost

Standard GitHub Macs are [free in public repositories](https://docs.github.com/en/billing/concepts/product-billing/github-actions). In a private repository they use up your plan's allowance, at [$0.062 a minute](https://docs.github.com/en/billing/reference/actions-runner-pricing) on 25 November 2026. Three Macs that hang until GitHub stops them, at 45 minutes each, would come to about $8.37.

GitHub's terms say hosted runners are for work on [the software project associated with the repository](https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#actions). Test pages that belong to your repository, not other people's sites.

## What we learned getting it to work

Two earlier versions failed, because Safari never opened.

- **"Booted" does not mean ready.** Right after booting, the load average (how many tasks want a processor) was between 360 and 742 on Macs with three processors. The workflow waits up to 10 minutes for it to drop. Two of the three Macs were still above 280 when Safari opened, so a shorter wait may be enough.
- **Start Safari before sending it the address.** `simctl openurl` can time out while Safari is still starting, so `simctl launch` goes first. The idea comes from [WordPress/gutenberg#84640](https://github.com/WordPress/gutenberg/pull/84640).
- **Look for the page request, not the exit code.** Others report that `openurl` can time out for a page that [then opens](https://github.com/graphty-org/graphty-monorepo/pull/3681). The workflow reads the web server's log instead.
- **Save the log early.** When a Mac hung, the run's log was lost, and once its saved files too. The boot log is uploaded before Safari starts.
- **Add 5 minutes to any time limit.** A job that will not stop runs for [another 5 minutes](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-cancellation) after `timeout-minutes`.

## Does it work every time?

Not known yet. On 25 November 2026 it passed on all three Macs in one run out of one.

It is shared as it is, without support, and changes to GitHub's Macs may break it. [MIT licence](LICENSE).
