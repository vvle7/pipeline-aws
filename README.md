# My First CI/CD Pipeline on AWS

**AWS Student Builder Group at University of Houston | DevOps & CI/CD Workshop**

In this lab you'll build a real CI/CD pipeline. Every time you change your website on GitHub, AWS Amplify automatically checks your code. If the checks pass, your site goes live. If they fail, nothing gets published and your old site stays up.

Everything happens in your web browser. You don't install anything.

**Time:** about 45 minutes. **Cost:** $0 on the AWS Free plan.

### Words you'll see
| Word | What it means here |
| --- | --- |
| **Repo** (repository) | A project folder on GitHub that remembers every change |
| **Commit** | Saving a change to your repo |
| **Build** | Amplify running the checks in `amplify.yml` |
| **Deploy** | Publishing your site so anyone can see it |
| **CI** (continuous integration) | Automatically checking every change |
| **CD** (continuous delivery/deployment) | Automatically publishing changes that pass the checks |

---

## Before the workshop (do this at home)

- [ ] **GitHub:** sign in at [github.com](https://github.com) on the laptop you'll bring. Keep your 2FA app or recovery codes handy.
- [ ] **AWS account:** create it at least **2 days early** at [aws.amazon.com](https://aws.amazon.com) (you need a card and a phone). Choose the **Free plan**.
- [ ] **Check it works:** sign in to the [AWS Console](https://console.aws.amazon.com), search for **Amplify**, and open it. If you see a message that your account is still being activated, wait and try again later.
- [ ] **Protect your account:** turn on MFA for your root user (**your account name** top right **> Security credentials > Assign MFA device**), then create a budget alert (**Billing and Cost Management > Budgets > Create budget > Zero spend budget**).
- [ ] **Charge your laptop.**

> If your AWS account isn't working on the day, pair up with a classmate and follow along on their screen. You can redo it on your own later with this guide.

---

## Step 1. Make your own copy of this project (3 min)

1. Make sure you're signed in to GitHub.
2. Go to the workshop repo: **https://github.com/uhawscloudclub/pipeline-starter-aws** (the page you're reading now). At the top, click the green **Use this template** button, then **Create a new repository**.
3. Fill in the form:
   - **Owner:** your GitHub username
   - **Repository name:** `my-first-pipeline`
   - **Visibility:** choose **Public** in the **Choose visibility** dropdown
4. Click **Create repository**.

✅ **You should see:** a page titled `your-username/my-first-pipeline` with three files: `README.md`, `amplify.yml`, and `index.html`.

From now on, work in **your** copy, not this page.

---

## Step 2. Connect your repo to AWS Amplify (10-15 min)

1. Open the [AWS Console](https://console.aws.amazon.com) and sign in.
2. In the **top-right corner**, click the Region name and choose **US East (N. Virginia) us-east-1**.
3. In the search bar at the top, type **Amplify** and click **AWS Amplify**.
4. Click **Create new app**. (Some accounts show **Deploy an app** or **Get started** instead. Click that.) If you see starter template cards, **don't pick one**. We're using your own repo.
5. Choose **GitHub** and click **Next**.
6. GitHub will open a pop-up. You may see two screens:
   - If GitHub asks you to sign in or enter a 2FA code, do it. That's normal.
   - **Authorize AWS Amplify:** click **Authorize AWS Amplify**.
   - **Install AWS Amplify:** choose **Only select repositories**, pick `my-first-pipeline`, then click **Install & Authorize**.
   - Nothing popped up? Your browser blocked it. Look for a blocked pop-up icon in the address bar, allow pop-ups, and click **Next** again.
7. Back in Amplify, under **Repository**, choose `my-first-pipeline`. Under **Branch**, choose `main`. Leave "My app is a monorepo" unchecked. Click **Next**.
8. On **App settings**, leave everything as it is. You should see that Amplify detected your `amplify.yml`. If it asks about a **service role**, keep the default (**Create and use a new service role**). Click **Next**.
9. On **Review**, click **Save and deploy**.
10. Wait 1-3 minutes. The deployment goes through **Provision > Build > Deploy**.
    - The Amplify page doesn't always update by itself. If it still says **Deploying** after a minute or two, **refresh the page** (F5, or Cmd+R on Mac).

✅ **You should see:** a green **Deployed** status and a link that looks like `https://main.xxxxxxxx.amplifyapp.com`. Click it. That's your live website!

---

## Step 3. Make your first change (5 min)

1. Go back to your repo on GitHub and click `index.html`.
2. Click the **pencil icon** (Edit this file) at the top right of the file.
3. Find `[Your Name]` and replace it with your name. Keep the rest the same.
4. Click the green **Commit changes...** button. In the box that opens, click **Commit changes** again.
5. Switch to the Amplify tab and click your app, then the **main** branch. A new deployment starts on its own. You didn't click anything in AWS. That's the pipeline.
6. When it says **Deployed**, open your site link and refresh. (Amplify page looks stuck? Refresh it.)

✅ **You should see:** your name on your live site.

---

## Step 4. Break it on purpose: a bug (7 min)

Real pipelines run checks so broken code never reaches users. Let's trigger one.

1. On GitHub, edit `index.html` again (pencil icon).
2. Delete this whole line:
   ```html
   <title>My First Pipeline</title>
   ```
3. Commit the change.
4. In Amplify, watch the new deployment **fail** (it turns red). If nothing changes after a minute or two, refresh the Amplify page.
5. Click the failed deployment, then click the **Build** step to open the log. **Scroll to the very bottom** and find the red **Build failed** line. The line **right above it** has the word **FAILED** and tells you what went wrong.
   - Every log line starts with a timestamp, like `2026-09-30T16:05:12Z [INFO]:`. That's normal, so read past it.
   - You'll also see FAILED inside lines that say `# Executing command`. That's Amplify showing the check before it runs, and it appears even on passing builds. Ignore those.
   - Log hard to read? Click **Download logs** and open the file.
6. Open your site and refresh. It still shows your last working version. The pipeline protected it.
7. Fix it: edit `index.html` and paste the title line back in, right under the `<meta charset="UTF-8">` line:
   ```html
     <title>My First Pipeline</title>
   ```
8. Commit. Watch the deployment go green again.

✅ **You should see:** `FAILED - index.html has no <title>` in the log, then a green deployment after your fix.

---

## Step 5. Break it on purpose: a leaked key (8 min)

Hackers scan GitHub for leaked cloud keys all day. Let's see the pipeline stop one.

1. Copy this **fake** key. AWS publishes it in its docs as an example, so it can't access anything:
   ```
   AKIAIOSFODNN7EXAMPLE
   ```
   **Never paste a real key anywhere.**
2. Edit `index.html` and paste the fake key on a new line anywhere inside `<body>`. Commit.
3. In Amplify, watch the deployment fail (refresh the page if it looks stuck). Open the **Build** log, scroll to the bottom, and look for the word **FAILED** right above **Build failed**.
4. Refresh your site. It did not update.
5. Delete the key from `index.html` and commit. Watch it go green.

✅ **You should see:** `FAILED - leaked AWS key found`, with the file name and line number of the key just above it.

> **The part that matters:** deleting the key did not un-leak it. It's still in your repo's history (click **Commits** on your repo and you'll find it), and your repo is public. If a **real** key ever leaks, your first move is to **deactivate it in AWS right away**, then clean up the code. This check only stops a bad deploy. Real teams also use secret scanners (GitHub push protection, gitleaks, TruffleHog) and short-lived credentials, so there's no long-lived key to leak in the first place.

---

## Stuck? Quick fixes

| Problem | Fix |
| --- | --- |
| No **Use this template** button | Sign in to GitHub first. |
| The GitHub pop-up never appeared | Allow pop-ups for the AWS site, then click **Next** again. |
| `my-first-pipeline` isn't in Amplify's repo list | Click **Update GitHub permissions** on that Amplify screen and add the repo. Or on GitHub: profile picture **> Settings > Applications > Installed GitHub Apps > AWS Amplify (us-east-1) > Configure**. |
| My Amplify app disappeared | Check the Region in the top-right corner. It must be **US East (N. Virginia)**. |
| "Your account is being activated" | Pair up with a classmate for today. Try again tomorrow. |
| My first deploy failed | Did you edit `amplify.yml`? Open the Build log, scroll to the bottom, and read the line right above **Build failed**. Ask a helper. |
| I see "FAILED" but my build passed | That's a `# Executing command` line, Amplify showing the check before it runs. A real failure always has a red **Build failed** line right below it. |
| I committed but no new deployment started | Make sure you edited the `main` branch. In Amplify, click the **main** branch and wait a minute. Still nothing? Ask a helper. |
| It says **Deploying** and never changes | Refresh the Amplify page (F5, or Cmd+R on Mac). The page doesn't always update on its own. |
| Stuck on **Provision** for over 5 minutes | The first build on a new account can be slow. Wait a bit longer, then ask a helper. |
| My site still shows the old version | Wait for **Deployed**, then hard refresh: **Ctrl+Shift+R** (**Cmd+Shift+R** on Mac). |
| I can't find the log | Click your app, then **main**, then the deployment, then the **Build** step. |

---

## Bonus challenges (finished early?)

1. **New file:** put the fake key in a new file called `config.js`. Is it still caught? Open `amplify.yml` and figure out why. (Why doesn't this README trigger it?)
2. **Your own check:** add a third command to `amplify.yml` that fails the build if your name isn't on the page. Hint: copy how check 1 works.
3. **Sneak attack:** try to get the key past the check without it matching. What does that tell you about simple pattern-based scanning?

---

## Put this on your résumé and LinkedIn

You built something real today. Here's how to show it without overselling it.

**Before you post, make it yours**
- Put your name on the page (Step 3) and keep the Amplify link working.
- Add one line to the top of this README: what the project is and your live site link.
- Recruiters click links. A live site plus a repo with a green build history beats any adjective.

**Résumé (Projects section)**

> **CI/CD Pipeline on AWS** | GitHub, AWS Amplify, YAML | [live site link]
> - Built a CI/CD pipeline that automatically tests and deploys a website to AWS Amplify Hosting on every push to GitHub
> - Configured automated quality and secret-scanning checks that block deployment when they fail, and verified them by pushing a broken page and a simulated leaked AWS access key

Did a bonus challenge? Add a third bullet for it, for example:
> - Wrote a custom build check that verifies required page content before every deploy

**LinkedIn (Profile > Add section > Projects)**
- **Name:** CI/CD Pipeline on AWS
- **Skills:** CI/CD, AWS Amplify, GitHub, DevSecOps
- **Link:** your live site and this repo
- **Description:**
> Built a CI/CD pipeline at the AWS Student Builder Group at University of Houston. Every push to GitHub runs automated checks in AWS Amplify, and the site only deploys if they pass. I tested it by pushing a broken page and a simulated leaked AWS key, and watched the pipeline block both. Biggest takeaway: a pipeline can stop a bad deploy, but a leaked key is still leaked, so the real fix is revoking it.

**Rules of thumb**
- Only claim what you did. If you paired with a classmate, rebuild it on your own account first (about 15 minutes with this README).
- Use numbers and results ("blocked 2 failing deploys"), not "familiar with" or "exposure to".
- Be ready to explain it in an interview: what CI and CD mean, what happens when a check fails, and why deleting a leaked key isn't enough.

## Clean up
Keep your Amplify app running if you link it on your résumé. Heads-up: an AWS Free plan account closes after 6 months unless you upgrade, and your live link goes with it. To remove the app, delete it in the Amplify console.
