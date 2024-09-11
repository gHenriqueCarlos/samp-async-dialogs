# samp-async-dialogs

Async dialog handling with PawnPlus tasks.

## Installation

Simply install to your project:

Include in your code and begin using the library:

```pawn
#include <async-dialogs>
```

It is recommended that you set a PawnPlus version explicitely on your pawn.json
(preferibly the latest) to avoid always downloading the latest one.

If you don't use sampctl, just download the `async-dialogs.inc` include and
drop it to your `includes/` folder, and then download the PawnPlus plugin and
include [from here](https://github.com/IllidanS4/PawnPlus/releases).

While you're on it and if you don't use PawnPlus yet,
[you should check it out!](https://github.com/IllidanS4/PawnPlus/blob/master/README.md)

## Usage

Functions
```pawn
ShowPlayerAsyncDialog(playerid, style, const caption[], const info[], const button1[], const button2[]);
ShowPlayerAsyncDialogStr(playerid, style, ConstStringTag:caption, ConstStringTag:info, ConstStringTag:button1, ConstStringTag:button2);
ShowPlayerAsyncDialogPages(playerid, dialogstyle, const caption[], info[], const button1[] = "Select", const button2[] = "Close", maxitemslist = 15, const nextbutton[] = "{FFA500}Next ->", const lastbutton[] = "{FF8C00}Back <-");
```

This will show the dialog and await for the response, which will pause the
current script's execution and return the yielded value to the last public
function (or 0 if it wasn't set). When it's responded to, the response details
will be inside the `dialog_response[e_DIALOG_RESPONSE_INFO]` array. If another
dialog gets shown while awaiting, the Task will be discarded with any following
code that was to be resumed.

### Example commands:
```pawn
// ShowPlayerAsyncDialog
CMD:asyncdialog(playerid, params[])
{
	task_yield(1);

	new dialog_response[e_DIALOG_RESPONSE_INFO];
	await_arr(dialog_response) ShowPlayerAsyncDialog(playerid, DIALOG_STYLE_LIST, "Example dialog", "This is listitem 0\nAnd this is one\nShow example nested dialog", "ok", "no");

	if(dialog_response[E_DIALOG_RESPONSE_Response])
	{
		if(dialog_response[E_DIALOG_RESPONSE_Listitem] == 2)
		{
			new other_dialog_response[e_DIALOG_RESPONSE_INFO];
			await_arr(other_dialog_response) ShowPlayerAsyncDialog(playerid, DIALOG_STYLE_MSGBOX, "Example nested dialog", "You will recieve a message\nwhichever your response is", "OK", "Yes");

			SendClientMessage(playerid, COLOR_WHITE, "You responded something to the example dialog");
		}
		else
		{
			SendClientMessageEx(playerid, COLOR_WHITE, "Recieved: listitem = %d | inputtext = %s", dialog_response[E_DIALOG_RESPONSE_Listitem], dialog_response[E_DIALOG_RESPONSE_InputText]);
		}
	}
	else
	{
		SendClientMessage(playerid, COLOR_WHITE, "Bye!");
	}
}
```
```pawn
CMD:asyncdialogstr(playerid, params[])
{
	task_yield(1);

	new dialog_response[e_DIALOG_RESPONSE_INFO];
	await_arr(dialog_response) ShowPlayerAsyncDialogStr(playerid, DIALOG_STYLE_LIST, @("Example dialog"), @("This is listitem 0\nAnd this is one\nShow example nested dialog"), @("ok"), @("no"));

	if(dialog_response[E_DIALOG_RESPONSE_Response])
	{
		if(dialog_response[E_DIALOG_RESPONSE_Listitem] == 2)
		{
			new other_dialog_response[e_DIALOG_RESPONSE_INFO];
			await_arr(other_dialog_response) ShowPlayerAsyncDialogStr(playerid, DIALOG_STYLE_MSGBOX, @("Example nested dialog"), @("You will recieve a message\nwhichever your response is"), @("OK"), @("Yes"));

			SendClientMessage(playerid, -1, "You responded something to the example dialog");
		}
		else
		{
			SendClientMessage(playerid, -1, "Recieved: listitem = %d | inputtext = %s", dialog_response[E_DIALOG_RESPONSE_Listitem], dialog_response[E_DIALOG_RESPONSE_InputText]);
		}
	}
	else
	{
		SendClientMessage(playerid, -1, "Bye!");
	}
}
```
```pawn
CMD:asyncdialogspages(playerid, params[]){
	task_yield(1);

	new list[8192] = "Column 1\tColumn 2\tColumn 3\n", tmp_str[64];
	for(new i = 0; i < 275; i++)
	{
		format(tmp_str, sizeof tmp_str, "Text\t%d\tIndex: %d\n", random(100) + i, i);
		strcat(list, tmp_str);
	}

	new dialog_response[e_DIALOG_RESPONSE_INFO];
	await_arr(dialog_response) ShowPlayerAsyncDialogPages(playerid, DIALOG_STYLE_TABLIST_HEADERS, "Page-dialog (pages: #currentpage/#pagelist)", list);

	if(dialog_response[E_DIALOG_RESPONSE_Response]){
		printf("listitem %d", dialog_response[E_DIALOG_RESPONSE_Listitem]);
		SendClientMessage(playerid, -1, "Recieved: listitem = %d | inputtext = %s", dialog_response[E_DIALOG_RESPONSE_Listitem], dialog_response[E_DIALOG_RESPONSE_InputText]);
	}else
	{
		SendClientMessage(playerid, -1, "Bye!");
	}
}
```

## Thanks
* @IllidanS4 for PawnPlus, which allows this and even more awesome stuff
* @TommyB123 for helping me test it out on a real environment


## Note
* I just took the dialog system with pages from @Robson04 and modified the code. Whether or not it's the best way to handle dialogs with pages, I simply adapted it to work.
* Dialog-Pages (Original): https://github.com/Robson04/dialog-pages
* Samp-async-dialogs (Original): https://github.com/AGraber/samp-async-dialogs

* Use this include to create dialogs with pages: https://github.com/Hreesang/samp-pagination-dialog (much much better)
