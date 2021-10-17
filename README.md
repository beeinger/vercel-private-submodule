# Vercel private submodule 🔒

Short and easy tutorial for using private submodules on Vercel

## 📋 Step by step 🚀

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

## 🎉 That's it! 🎉

<br/>

### + Post Scriptum

I haven't added support for multiple submodules but it should be easy enough, try edditing this part and then the rest of the script.

```bash
# get submodule commit
output=`git submodule status --recursive` # get submodule info
no_prefix=${output#*-} # get rid of the prefix
COMMIT=${no_prefix% *} # get rid of the suffix
```