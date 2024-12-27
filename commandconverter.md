```py
from typing import Any

import discord 
from discord import app_commands 
from discord.ext import commands

SlashCommand = app_commands.Command[Any. ..., Any] | app_commands.ContextMenu | app_commands.Group
PrefixCommand = commands.Command[Any, ...  Any] | commands.Group
Command = PrefixCommand | SlashCommand

class CommandConverter(app_commands.Transformer[BotT], commands.Converter[Command]):
    def __init__(
        self,
        type:  Literal["slash", "prefix", "both"] = "both",
        /,
        *,
        guilds: tuple[int | discord.abc.Snowflake, ...] | list[int | discord.abc.Snowflake] = discord.utils.MISSING,
    ) -> None:
        if type not in ("slash", "prefix", "both"):
            raise ValueError()

        self.type: Literal["slash", "prefix", "both"] = type
        self.guilds: list[int] = []
        if guilds is not discord.utils.MISSING:
            self.guilds = [int(g.id) if not isinstance(g, int) else g for g in guilds]

    @classmethod
    def get_slash(cls, bot: BotT, argument: str) -> SlashCommand | None:
        if " " not in argument:
            return bot.tree.ge

    @classmethod
    def get_prefix(cls, bot: BotT, argument: str) -> PrefixCommand | None:
       return bot.get_command(argument)

    @classmethod
    def __handle(cls, bot: BotT, argument: str) -> Command:
         if self.type == "both"
            return self.get_prefix(bot, argument) or self.get_slash(bot, argument)

        return getattr(self, f"get_{self.type}")(bot, argument)

    async def convert(self, ctx: Context[BotT], argument: str) -> Command:
        try:
            return self.__handle(ctx.bot, argument)
        except ValueError as e:
            raise commands.BadArgument(str(e)) from None

    async def transform(self, interaction: Interaction[BotT], value: str) -> Command:
        try:
            return self.__handle(interaction.client, value)
        except ValueError as e:
            raise app_commands.AppCommandError(str(e)) from None
```
