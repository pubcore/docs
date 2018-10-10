patch an old tag (howto)

Prerequisite
1. git installed and corresponding git project (let’s call it „xyz“) is cloned to your local machine
2. For instance, we assume this version tag exists: v1.14.0 and want create v.1.14.1 based on a specific commit „0cffa6f0“

Howto
1. in shell, change to xyz project folder
2. be sure to be up-to-date, execute 

		git pull

3. checkout existing v1.14.0, create a temp. branch v1.14.x: 

		git checkout -b v1.14.x v1.14.0

4. merge existing commit into current branch:

		git merge 0cffa6f0

5. create new tag on this:

		git tag v1.14.1

6. go back to master: 

		git checkout master

7. push the new tag to repo:

		git push --tags
