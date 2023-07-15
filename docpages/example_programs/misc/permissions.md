\page checking-permissions Checking Permissions 

Before you allow any server member to execute your commands, you might want to check whether they have certain permissions, such as the Ban Members permission, or any of the other permissions as listed [in the Discord documentation](https://discord.com/developers/docs/topics/permissions#permissions).

This example shows a simple check for the `dpp::p_ban_members` permission, but can be adapted to check for other permissions as well

~~~~~~~~~~{.cpp}
#include <dpp/dpp.h>

int main(){
    dpp::cluster bot("token");
	 
    bot.on_log(dpp::utility::cout_logger());
    
    /* The event is fired when someone issues your commands */
    bot.on_slashcommand([&bot](const dpp::slashcommand_t & event) {
        /* Check which command they ran */
        if (event.command.get_command_name() == "permission") {
            /* The member invoking this command */
            dpp::guild_member member = event.command.member; 

            /* Get all of this member's permissions, including overwrites for this channel */
            dpp::permission member_permissions = event.command.channel.get_user_permissions(member); 

            /* Check if their permissions include the permission to ban members, and reply to the command accordingly */
            if(member_permissions.has(dpp::p_ban_members)){
                event.reply(":white_check_mark: You have the permissions required to ban guild members.");
            } else {
                event.reply(":no_entry_sign: You do not have the permissions required to ban guild members.");
            }
        }
    });
    
    bot.on_ready([&bot](const dpp::ready_t & event) {
        if (dpp::run_once<struct register_bot_commands>()) {
    
            /* Create a new global command on ready event */
            dpp::slashcommand newcommand("permission", "Checks whether a user has permission to ban guild members", bot.me.id);
    
            /* Register the command */
            bot.global_command_create(newcommand);
        }
    });

    // false: Don't go back to main after starting bot 
    bot.start(false); 
    return 0;
}
~~~~~~~~~~

![image](https://github.com/brainboxdotcc/DPP/assets/84308084/64b6d107-6785-4a1c-8db8-223ed5349962)
