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
git add . && git commit -m "Initial commit.(A)" # Please not A, we use it for diagram
git push -u origin [custombranch] 
```

