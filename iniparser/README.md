#Add libiniparser to parser ini settings file

# /sdcard/miui_recovery/setttings.ini
-------------------------------------------------------------------
```
#include "../../../iniparser/iniparser.h" // for iniparser 

dictionary * ini;

int load_settings()
{
	struct stat st;

    miuiIntent_send(INTENT_MOUNT, 1, "/sdcard");
    if (stat("/sdcard/miui_recovery", &st) != 0) {
    miuiIntent_send(INTENT_SYSTEM, 1, "mkdir -p /sdcard/miui_recovery");
    }
    ini = iniparser_load("/sdcard/miui_recovery/settings.ini");
    if (ini==NULL)
        return 1;
    
    return 0;
}

void write_settings()
{
    char *ini_name;
    ini_name = "/sdcard/miui_recovery/settings.ini";
    iniparser_dump_ini(ini, ini_name);
    iniparser_freedict(ini);
}

```
------------------------------------------------------------------


```
static STATUS set_md5sum_state(struct _menuUnit* p) {

	int currstatus;
	if (1 == load_settings()) {
		return MENU_BACK;
	}
	currstatus = iniparser_getboolean(ini, "zipflash:md5sum", -1);
	char *statusdlg;
	char *btntext;

	if (currstatus == 0) {
		statusdlg = "Generate md5sum are currently disabled.";
		btntext = "Enable";
	} else {
		statusdlg = "Generate md5sum are currently enabled.";
		btntext = "Disable";
	}

if (RET_YES == miui_confirm(5, p->name, statusdlg, p->icon, btntext, "Cancel")) {
        if (currstatus == 0) {
            // enable md5sum
            iniparser_set(ini, "zipflash:md5sum", "1");
        } else if (currstatus == 1) {
            // disable md5sum
            iniparser_set(ini, "zipflash:md5sum", "0");
        }
        write_settings();
    }
    return MENU_BACK;
}
```

