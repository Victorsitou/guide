# Initial files

Once you [add your bot to a server](../000-prerequisites/003-inviting-your-bot.md), the next step is to start coding and get it online! Let's start by creating a `.env` file for your client token and a main file for your bot application.

## Creating configuration files

As explained in the "What is a token, anyway?" section, your token is essentially your bot's password, and you should protect it as best as possible. This can be done through a `.env` file, or by using environment variables.

Open your application in the [Discord Developer Portal]({{ devportal }}) and go to the `Bot` page to copy your token.

### Using environment variables

Environment variables are special values for your environment (e.g., terminal session, docker container, or environment variable file). You can pass these values into your code's scope so that you can use them.

!!! Note Inline End

    When referring to a `.env` file, keep in mind that you can name this file whatever you prefer. For example, the file can be named `token.env` or `secret.env`.

Storing data in a `.env` file is a common way of keeping your sensitive values safe. Create a `.env` file in your project directory and paste in your token. You can access your token inside other files by using `os.getenv`.

=== "secret.env"

    ``` python
    # Each line in a .env file should hold a KEY = value pair.
    YOUR_BOT_TOKEN = OTA4MjgxMjk4NTU1MTA5Mzk2.YYzc4A.TB7Ng6DOnVDlpMS4idjGptsreFg
    ```

=== "main.py"

    ``` python
    # Importing "os" module.
    import os

    # Getting .env value.
    # You can name this variable in the script however you like.
    YOUR_BOT_TOKEN = os.getenv("YOUR_BOT_TOKEN")
    ```

!!! Danger

    If you're using Git, you should not commit this file and should ignore it via `.gitignore`.

You can alternatively also use the [python-dotenv package][python-dotenv] to either load the env variables into the environment, or make a `config` dict out of the env values.

=== "load_dotenv()"

    ``` python
    import os
    from dotenv import load_dotenv

    load_dotenv()  # Take environment variables from .env.

    # Using the variables in your application, which uses environment variables
    # (e.g. from 'os.environ()' or 'os.getenv()')
    # as if they came from the actual environment.
    YOUR_BOT_TOKEN = os.getenv("YOUR_BOT_TOKEN")
    ```

=== "dotenv_values()"

    ``` python
    from dotenv import dotenv_values

    config = dotenv_values(".env")  # Makes a dict out of the values.

    # Thus, we get
    # config = {YOUR_BOT_TOKEN: OTA4MjgxMjk4NTU1MTA5Mzk2.YYzc4A.TB7Ng6DOnVDlpMS4idjGptsreFg}
    # which can be used as:
    YOUR_BOT_TOKEN = config["YOUR_BOT_TOKEN"]
    ```

Keep in mind that the values imported from the `.env` file are **in string format**. Therefore if you would like to, say, use them for calculations, you'll have to convert them via `int()`

??? Info "Online editors (Glitch, Heroku, Replit, etc.)"

    While we generally do not recommend using online editors as hosting solutions, but rather invest in a proper virtual private server, these services do offer ways to keep your credentials safe as well! Please see the respective service's documentation and help articles for more information on how to keep sensitive values safe:

    - Glitch: [Storing secrets in .env][glitch-article]
    - Heroku: [Configuration variables][heroku-article]
    - Replit: [Secrets and environment variables][replit-article]

### Git and `.gitignore`

Git is a fantastic tool to keep track of your code changes and allows you to upload progress to services like [GitHub][github], [GitLab][gitlab], or [Bitbucket][bitbucket]. While this is super useful to share code with other developers, it also bears the risk of uploading your configuration files with sensitive values!

You can specify files that Git should ignore in its versioning systems with a `.gitignore` file. Create a `.gitignore` file in your project directory and add the names of the files and folders you want to ignore:

```
__pycache__/
secrets.env
config.json
```

!!! Tip

    [`__pycache__/`][pycache] has been included in `.gitignore` as it is simply cache that helps loading and running your script faster (this is an oversimplification). As it is of no particular importance, and is recompiled every time a change is made in the script, it is better to not commit the directory.

    Also, you can specify quite intricate patterns in `.gitignore` files, as per the requirements of your project. Check out the [Git documentation on `.gitignore`][gitignore] for more information!

## Creating the main file

Open your code editor and create a new file. We suggest that you save the file as `main.py` or `bot.py`, but you may name it whatever you wish.

Here's the base code to get you started:

``` python linenums="1" title="main.py"
# Import the necessary libraries.
import disnake
from disnake.ext import commands

# Creating a commands.Bot() instance, and assigning it to "bot"
bot = commands.Bot()

# When the bot is ready, run this code.
@bot.event
async def on_ready():
    print("The bot is ready!")


# Login to Discord with the bot's token.
bot.run(YOUR_BOT_TOKEN)
```

This is how you create a client instance for your Discord bot and login to Discord. Open your terminal and run `python3 main.py` to start the process. If you see "The bot is ready!" after a few seconds, you're good to go!

!!! Tip

    After closing the process with `Ctrl + C`, you can press the up arrow on your keyboard to bring up the latest commands you've run in the terminal. Pressing up and then enter after closing the process is a quick way to start it up again.

## Resulting code

If you want to compare your code to the code we've constructed so far, you can review it over on the GitHub repository [here]({{ guiderepo }}/tree/main/docs/extra-code-samples/initial-files).



[python-dotenv]: https://pypi.org/project/python-dotenv/
[glitch-article]: https://glitch.happyfox.com/kb/article/18
[heroku-article]: https://devcenter.heroku.com/articles/config-vars
[replit-article]: https://docs.replit.com/repls/secrets-environment-variables

[github]: https://github.com/
[gitlab]: https://about.gitlab.com/
[bitbucket]: https://bitbucket.org/product
[pycache]: https://stackoverflow.com/questions/16869024/what-is-pycache
[gitignore]: https://git-scm.com/docs/gitignore