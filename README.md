## why is this fork?
Google Message used to be my default SMS app, which blocks SMS sent by specific number. While more proper way is by the content of the message, cause these spam messages don't have fixed number.

From Android 4.4, only the default SMS app could receive system broadcast `SMS_DELIVER_ACTION` when a new SMS arrive. And `SMS_RECEIVED_ACTION` could be received by all apps. So I have to do some modification on the default one.

There are some SMS apps out there with block function, but I just dont trust them. And rewriting such an app seems not worth it, SMS is simple huh, try to think about MMS.

AOSP seems to be the best choice.

## how does it work?
When a message received, `ReceiveSmsMessageAction` inserts the message into the telephony db and sounds a notification to user.

    /**
     * Entry point for posting notifications.
     * Don't call this on the UI thread.
     * @param silent If true, no ring will be played. If false, checks global settings before
     * playing a ringtone
     * @param coverage Indicates which notification types should be checked. Valid values are
     * UPDATE_NONE, UPDATE_MESSAGES, UPDATE_ERRORS, or UPDATE_ALL
     */
    public static void update(final boolean silent, final int coverage) {
        update(silent, null /* conversationId */, coverage);
    }

It's up to you wheather the message should be silenced.

## how to compile?
	
	source build/envsetup.sh 
	cd packages/apps/Messaging
	// 9 should be <number of cores> + 1, just be make it faster
	mma -j9

    // check log info to locate the output apk
    adb install -r .../.../messaging.apk

## other change in Android.mk
    
    // from <platform> to <testkey>
    // platform signed apk couldn't be installed in normal way
    LOCAL_CERTIFICATE := testkey

    // don't strip the classes.dex file from the apk 
    DEX_PREOPT_DEFAULT := nostripping

## try at your own risk 🍻