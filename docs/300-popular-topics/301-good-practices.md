# Good practices

Here's a list of some good practices in disnake.

## Running code when a cog is loaded

Most people are used to running everything in `__init__` but that doesn't allow running async code. In this case you can overwrite the special `cog_load` method. 

```py
class MyCog(commands.Cog):
    def __init__(self, bot: commands.Bot):
        self.bot = bot

    async def cog_load(self):
        self.data = await bot.fetch_database_data()

    @commands.slash_command()
    async def command(self, interaction: disnake.ApplicationCommandInteraction, user: disnake.User):
        await interaction.response.send_message(self.data[user.id])
```

## Reloading your bot

A one of the less known disnake features is the `reload` kwarg for your bot. It reloads extensions every time they are edited allowing you to test your code in real-time.
This is especially useful if startup times for your bot are very slow since only one extension will be reloaded at a time.

```py
from disnake.ext import commands

bot = commands.Bot(..., reload=True)
```

!!! Warning

    This should be used purely for debugging. Please do not use this in production.

## Attaching Embed Files

Using explicit `attachment://` used to be the only way to attach a local file to an embed. Disnake abstracts this with `file=` kwargs.

```py
# discord.py:
embed = discord.Embed(title="An embed with an image")
embed.set_image(url="attachment://image.png")

file = discord.File("assets/image.png")

await channel.send(embed=embed, file=file)
```

```py
# disnake:
embed = disnake.Embed(title="An embed with an image")
embed.set_image(file=disnake.File("assets/image.png"))

await channel.send(embed=embed)
```

## Converting arguments in commands

discord.py used to have `Converter` classes to convert arguments if they are provided. These were however very hard to use with type-checkers because the type of the parameter is never actually the converter itself.

Disnake aims to eliminate this issue by only allowing conversion of builtin types like `disnake.Member`, `disnake.Emoji`, etc. If you ever want to have your own converter you have to use the `converter` argument in `Param`

```py
# discord.py
class DataConverter(commands.Converter):
    async def convert(self, ctx: commands.Context, arg: str):
        parts = arg.split(",")
        return {"a": parts[0], "b": int(parts[1]), "c": parts[2].lower()}


@commands.command()
async def command(self, ctx: commands.Context, data: DataConverter):
    ...
```

```py
# disnake
async def convert_data(inter: disnake.ApplicationCommandInteraction, arg: str):
    parts = arg.split(",")
    return {"a": parts[0], "b": int(parts[1]), "c": parts[2].lower()}


@commands.slash_command()
async def command(
    self,
    inter: disnake.ApplicationCommandInteraction,
    data: Dict[str, Any] = commands.Param(converter=convert_data),
):
    ...
```

If you absolutely want to be able to use classes you may pass in a class method
```py
class Data:
    def __init__(self, a: str, b: int, c: str):
        self.a = a
        self.b = b
        self.c = c

    @classmethod
    async def from_option(cls, inter: disnake.ApplicationCommandInteraction, arg: str):
        parts = arg.split(",")
        return cls(parts[0], int(parts[1]), parts[2].lower())


@commands.slash_command()
async def command(
    self,
    inter: disnake.ApplicationCommandInteraction,
    data: Data = commands.Param(converter=Data.from_option),
):
    ...
```

## Context command targets

Instead of using `inter.target` you should be using a parameter of your command.

```py
@commands.user_command()
async def command(self, inter: disnake.ApplicationCommandInteraction, user: disnake.User):
    await inter.response.send_message(f"Used on {user.mention}")
```

## Slash command descriptions

You may use docstrings for command and option descriptions. Everything before `Parameters` is the command description. Everything after `Parameters` are the option descriptions.

```py
@commands.slash_command()
async def command(
    self,
    inter: disnake.ApplicationCommandInteraction,
    category: str,
    item: str,
    details: bool,
):
    """Show item info

    Parameters
    ----------
    category: The category to search
    item: The item to display
    details: Whether to get the details of this time
    """
```

## Guild-only commands

While disnake does provide a `@commands.guild_only()` decorator, it still makes you handle `guild` being optional in case you're using linters. To solve this you should be using `GuildCommandInteraction`.

```py
# before
@commands.slash_command()
@commands.guild_only()
async def command(inter: disnake.ApplicationCommandInteraction):
    assert inter.guild is not None
    await inter.send(inter.guild.name)


# after
@commands.slash_command()
async def command(inter: disnake.GuildCommandInteraction):
    await inter.send(inter.guild.name)
```
