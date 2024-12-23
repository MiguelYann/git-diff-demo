# Git diff demo

The goal of this projet is a basic example that aims to understand git diff command(focus on two and three dot) A..B and A...B. And thus view how git host repository interprets it(Focus on github and gitlab)


## Git basics

We have the notion of ranges. That is representby two and three dot. It can be apply both on git log and git diff, but we different goals.

### Log

> In this example 1 and 2 can be commit, branch or tag.

```shell
git log 1..2
```

* It means log commits ***on 2 that is not in 1*** from the common ancestor.

![logTwoDot](./1..2.png)


```shell
git log 1...2
```

* It means log commits that is not both ***on 1 AND on 2***. Every commit that in A and B does not appear.

![threeTwoDot](./1...2.png)


> On image the red space will not be log.


### Diff

> In this example 1 and 2 can be commit, branch or tag.

```shell
git diff 1..2
```
* It means show last change difference between 1 and 2, base on 2. So **changes both made in 1 and 2 but in perspective on 2**

![diffTwoDot](./diff(1..2).png)

```shell
git diff 1...2
```

* It means show only changes from 2 with common ancestor of 1 and 2. So **changes only appears on 2 since 2 has diverge from A(Usually for Pull/Merge request).

![diffThreeDot](./diff(1...2).png)


> On image just white commit C0' and 2 appears. 


## Setup 

### Remotes

* For host repository behaviour please create two reposiotry, one on Github and another on Gitlab

```shell
git remote set-url --add --push origin [gitHubURLRepo]
git remote set-url --add --push origin [gitlabURLRepo]
```

### Init on main branch

* Create on main branch a README.md file
* Just commit it 
* Push on both host(Github and gitlab)

```shell
git switch -c [custombranch] # Here is master
echo "First commit with readme" > README.md
git add . && git commit -m "Initial commit.(A)"
git push -u origin [custombranch] 
```

### Create first feature branch

#### Setup 

* This branch add 3 changes
    * In README.md
    * Create directory and:
        * New file feature-1.txt with random text
        * New image for explanation(master_with_feature_1)

* The commit message is 'feature 1(B)'

```shell
git switch -c feature-1 
mkdir -p feature-1
echo "First commit with readme" | tee README.md feature-1/feature-1.txt
mv [anImage] feature-1
# Add every number of commit you want, in y case i made 
git add . && git commit -m "feature 1(B)"
git push -u origin feature-1 
```

At this stage we can represent what we have done:

![master_feat_1](./feature-1/master_with_feature_1.png)

#### Analyse two dot diff/log use-case

| Host   | git log main..feature-1                                   | git diff main..feature-1             | Comments                  |    
| ------ |                  ------                                   |                 ------               | ------                    |
|  Gitlab | Expected behaviour: ✅  ![gitlabtwodofflog](./feature-1/glgitlogtwodot.png)      |                 Expected behaviour: ✅  ![gitlabgitdifftwodot](./feature-1/gitlabgitdifftwodot.png)           | - We see exact 3 commits like git command on shell <br> - We remark a thing on radio button we need to select *Include changes to target since source was created*  <br> - The URL contains nomenclature main..main..feature-1 <br> - ⚠️ We have a none intuitive stuff before version 16.2(Just 'resolve' at last year.), [see all interesting conversations about that](https://gitlab.com/gitlab-org/gitlab/-/issues/377957) <br> - In my opinion I feel happy Gitlab team take a time to design a better solution. Although we have a mental modal to map the notion of(source and target) and URL that have git command(A..B)   |
| Github | Expected behaviour: ⛔️  ![githubgitlogtwodot.png](./feature-1/githublogtwodot.png)      |     Expected behaviour:  ✅  ![githubgitdifftwodot.png](./feature-1/githubgitdifftwodot.png)         |  - We also have same 3 commits log like both shell and gitlab <br> - We have a subtility here with notion of *base* and URL contains three dot by default ![githubgitlogtwodot.png](./feature-1/githublogthreedot.png)<br> - Note that we see code in red in difference, it is in master and not feature-1, and code in green it is the inverse. <br> - We can change the URL with two dot to see result and we see that GH does not display commit and does not support this feature in UI as Gitlab does. We need to explicit add on URL |
| local     | Expected behaviour: ✅    ![shelltwodofflog](./feature-1/gitlogtwodot.png)    |    Expected behaviour: ✅   ![gitdiffshelltwodotfirst](./feature-1/gitdiffshelltwodotfirst.png)  and  ![gitdiffshelltwodotsecond](./feature-1/gitdifftwodotshelltwo.png) |     - We see all files that have add and change(9) <br> - In my opinion git have inconstency between log and diff in internal system with range. I feel confident with this [comment on maintainer of git](https://gitlab.com/gitlab-org/gitlab/-/merge_requests/104175#note_1181397081) that confirm that assertion and all confusion |


#### Analyse three dot diff/log use-case

* We create a branch ``feature-2`` at the same time we basic change just to add another commit on master(we keep no-fast forward merge stratey)
* So in master we have two new commits that diverge from ``feature-1``. 

We can see it like that:

![feat2-master](./merge-feat2-main.png)

So C' is the merge commit that have children C(Commit from feat-2) and previous HEAD(initial commit A).

At this point we see that commit A (white) is the common ancestor between `main` and `feature-1`, but all color's commit is for each branch.

Let's try to play with three dot analyse

| Host   | git log main...feature-1                                   | git diff main...feature-1             | Comments                  |    
| ------ |                  ------                                   |                 ------               | ------                    |
|  Gitlab | Expected behaviour: ❌  ![gitlabgitlogthreedot](./gitlabgitlogthreedot.png)      |                 Expected behaviour: ✅  ![gitlabgitdiffthreedot](./gitlabgitdiffthreedot.png)           | - (log)We just 3 commits like(..), tha's not match git standard.This would output 5! <br> - (ui behaviour)We see a thing on radio button; if we select *Include changes to target since source was created*  like previously we have same number of commit and URL is map with (`..`). If we select *Only incoming changes from source* we see that URL contains now(``...``) that we expected.  <br> -(diff) We have 17 files changes as expected. If we choose option with (..) we see that here we have 18 files just because comparision here include ``main`` branch ![gitlabgitdiffdemotwo](./gitlabgitdiffdemotwo.png)  <br> - The URL contains nomenclature main...feature-1 <br> - ⚠️ We have a none intuitive stuff before version 16.2(Just 'resolve' at last year.), [see all interesting conversations about that](https://gitlab.com/gitlab-org/gitlab/-/issues/377957)<br> (diff)- <br> - In my opinion I feel happy Gitlab team take a time to design a better solution. Although we have a mental modal to map the notion of(source and target) and URL that have git command(A..B)   |
| Github | Expected behaviour: ❌  ![githubgitlogthreedot.png](./githubgitlogthreedot.png)      |     Expected behaviour:  ✅  ![githubgitdiffthreedot.png](./githubgitdiffthreedot.png)         |  - (log)We also have same 3 commits log like Gitlab and git command <br> - (diff) We also have 17 files changes like both gitlab and git command  |
| local     | Expected behaviour: ✅    ![localgitlogthreedot](./localgitlogthreedot.png)    |    Expected behaviour: ✅   ![localgitdiffthredot](./localgitdiffthredotfirst.png)  and  ![localgitdiffthredot](././localgitdiffthredotsecond.png) |     - (log) We have 5 commits, 2 from `main` and 3 from `feature-1`. As expected we don't have common commit A. <br> (log)This log it's not very useful because it see just commit for both branches <br> -(diff) We have 17 files just belongs to ``feature-1`` branch changes <br> - (log/diff)In my opinion git have inconstency between log and diff in internal system with range. I feel confident with this [comment on maintainer of git](https://gitlab.com/gitlab-org/gitlab/-/merge_requests/104175#note_1181397081) that confirm that assertion and all confusion |