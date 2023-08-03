# Vercel private submodule 🔒

Short and easy tutorial for using private submodules on Vercel

## 💡 [Approach 1 - .gitmodules]
### 📋 Instructions 🚀

Edit your `.gitmodules` to follow this schema: 

```
[submodule "<repo-name>"]
path = <folder-name>
url = https://<github-token>@github.com/<owner-name>/<repo-name>.git
```

**The important part is the `<github-token>` in `https://<github-token>@github.com/<owner-name>/<repo-name>.git`**

You can use either a classic token or a fine-grained token with necessary permissions.

> 💡 TIP: If you dont know how to get it [have a look at this](https://docs.github.com/en/enterprise-server@3.6/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)


### 🤫 If you don't want to commit the token

Use this trick - add `.gitmodules` to `.gitignore` and then put the whole contents of the `.gitmodules` in a GitHub Secret or a secret on Vercel, and then when installing dependencies and doing setup, do smthn like `echo '${{ secrets.GITMODULES }}' > .gitmodules`

### 🔐 Best to use a fine-grained token with readonly access only to the repos you have as submodules.

### 🎉 That's it! 🎉

> 🤝🏽 Thanks [@FrameMuse](https://github.com/FrameMuse) [Issue: [#7](https://github.com/beeinger/vercel-private-submodule/issues/7)]

<br/>

## 💡 [Approach 2 - Script] 

### 📋 Step by step 🚀

- copy the ./vercel-submodule-workaround.sh to the root of your project

- make sure the file is executable - run this command:

    ```bash
    chmod u+x vercel-submodule-workaround.sh
    ```

- in your package.json add a script:

    ```json
    {
        "scripts":{
            "vercel-install": "./vercel-submodule-workaround.sh && yarn --ignore-scripts --production=false",
        }
    }
    ```
    ***Info:*** 
    
    **--production=false** ensures that dev-dependencies will be installed, if you dont need your dev-dependencies to install then just delete this flag.

    **--ignore-scripts** ignores pre and post install scripts if you want the scripts to run delete this flag.

- Tell Vercel to use vercel-install instead of the defalt install command

    - project -> Settings -> General => Build & Development Settings

    - in INSTALL COMMAND type 
    
    ```bash 
    yarn vercel-install 
    # or 
    npm vercel-install
    ```

    - toggle OVERRIDE on

- Create a Github access token

    - Settings -> Developer settings -> Personal access tokens

    - Generate new token
     
    - set a note (eg. vercel)

    - set expiration to whatever you see fit (eg. No expiration)

    - select repo
    
        [x] repo - Full control of private repositories
    
    - copy the generated token

- Add the token to Vercel Environment Variables

    - Project -> Settings -> Environment Variables

    - add a new variable called GITHUB_ACCESS_TOKEN and with value of the token you just copied

### 🎉 That's it! 🎉

<br/>

### + Post Scriptum

I haven't added support for multiple submodules but it should be easy enough, try edditing this part and then the rest of the script.

```bash
# get submodule commit
output=`git submodule status --recursive` # get submodule info
no_prefix=${output#*-} # get rid of the prefix
COMMIT=${no_prefix% *} # get rid of the suffix
```
