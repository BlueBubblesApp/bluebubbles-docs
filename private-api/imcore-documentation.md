---
description: Here's what we've found and learned while messing with IMCore
---

# IMCore Documentation

## Chats

### Getting a Chat Object

Chat objects are defined as `IMChat`, and we access these via a lookup by `guid`.

To do this, you'll want to add the `IMChatRegistry.h` header into your project.

```objectivec
// Retrieve a IMChat instance from a given guid
//
// Uses the chat registry to get an existing instance of a chat based on the chat guid
+(IMChat *) getChat: (NSString *) guid {
    if(guid == nil) return nil;

    IMChat* imChat = [[IMChatRegistry sharedInstance] existingChatWithGUID: guid];
    return imChat;
}
```

### Start/Stop Typing in a Chat

This one is relatively simple:

```objectivec
[chat setLocalUserIsTyping:YES];
[chat setLocalUserIsTyping:NO];
```

### Are we Receiving a Typing Indicator?

This one is also pretty simple:

```objectivec
chat.lastIncomingMessage.isTypingMessage
```

This will provide a `bool` value. However, this won't provide you with live updates on typing status. To do this, you'll need to make use of ZKSwizzle (read more about this in [#contribution-resources](building-and-contributing.md#contribution-resources "mention")).

### Listen to Typing Indicator Status

```objectivec
// Credit to w0lf
// Handles all of the incoming typing events
ZKSwizzleInterface(BBH_IMMessageItem, IMMessageItem, NSObject)
@implementation BBH_IMMessageItem

- (BOOL)isCancelTypingMessage {
    // isCancelTypingMessage seems to also have some timing issues and adding a delay would fix this
    // But I would rather not rely on delays to have this program work properly
    //
    // We would rather that the typing message be cancelled prematurely rather
    // than having the typing indicator stuck permanently
    NSString *guid = [self getGuid];

    if(guid != nil) {

        if([self isLatestMessage]) {
            // handle stopped typing status here
        }
    }
    return ZKOrig(BOOL);
}

- (BOOL)isIncomingTypingMessage {
    // We do this because the isIncomingTypingMessage seems to have some timing
    // issues and will sometimes notify after the isCancelTypingMessage so we need to confirm
    // that the sender actually is typing
    [self updateTypingState];

    // This is here to ensure that no infinite typing occurs
    // If for whatever reason the isCancelTypingMessage does not occur,
    // this should catch the error in 2 seconds
    double delayInSeconds = 2.0;
    dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
    dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
        if(self != nil) {
            NSString *guid = [self getGuid];
            if(guid != nil) {
                if([BlueBubblesHelper isTyping:guid] == NO) {
                    // handle stopped typing status here
                }
            }
        }

    });

    return ZKOrig(BOOL);
}

// Check to see if this IMMessageItem matches the last IMChat's message
// This helps to avoid spamming of the tcp socket
- (BOOL) isLatestMessage {
    NSString *guid = [self getGuid];
    // Fetch the current IMChat to get the IMMessage
    IMChat *chat = [BlueBubblesHelper getChat:guid];
    IMMessageItem *item = (IMMessageItem*) self;
    IMMessage *message = item.message;
    if(message.isFromMe) return NO;

    // If the IMChat's last message matches our own IMMessage, then we can proceed
    // this should avoid spamming of the tcp socket
    return chat.lastIncomingMessage.guid == message.guid;
}

// Update the typing state by checking the message state
- (void) updateTypingState {
    if(![self isLatestMessage]) return;

    NSString *guid = [self getGuid];

    // If we failed to get the guid for whatever reason, then we can't do anything
    if(guid != nil) {
        IMChat *chat = [BlueBubblesHelper getChat:guid];
        // Send out the correct response over the tcp socket
        if(chat.lastIncomingMessage.isTypingMessage == YES) {
            // handle started typing status here
        } else {
            // handle stopped typing status here
        }
    }
}

@end
```

In the above code, we use swizzling techniques to "intercept" when iMessage calls the `isCancelTypingMessage` and `isIncomingTypingMessage` functions. Due to some timing issues described in the code comments, we double verify to make sure the user has actually started or stopped typing, and then pass that status back to clients.

### Mark Chat Read

Like the rest, this is fairly straightforward:

```objectivec
[chat markAllMessagesAsRead];
```

This will remove the unread dot from macOS iMessage.

### Mark Chat Unread

Like the rest, this is fairly straightforward:

```objectivec
[chat markLastMessageAsUnread];
```

This will add an unread dot to the chat in macOS iMessage.

{% hint style="warning" %}
This method requires macOS Ventura (13.0) or above!
{% endhint %}

### Change Group Chat Name

Once again, another easy method:

```objectivec
[chat _setDisplayName:(@"new name here")];
```

### Add or Remove Participants from a Group Chat

```objectivec
NSArray<IMHandle*> *handles = [[IMHandleRegistrar sharedInstance] getIMHandlesForID:(@"some address here")];

// when removing participants, you don't need to do this if block
if (handles != nil) {
    IMAccountController *sharedAccountController = [IMAccountController sharedInstance];
    IMAccount *myAccount = [sharedAccountController mostLoggedInAccount];
    IMHandle *handle = [[IMHandle alloc] initWithAccount:(myAccount) ID:(@"some address here") alreadyCanonical:(YES)];
    handles = @[handle];
}

if([chat canAddParticipants:(handles)]) {
    // to add
    [chat inviteParticipantsToiMessageChat:(handles) reason:(0)];
    // to remove
    [chat removeParticipantsFromiMessageChat:(handles) reason:(0)];
}
```

First, you'll want to pass addresses to `IMHandleRegistrar` so you can get `IMHandle` objects for them.&#x20;

Then make sure those handles are added to the `IMAccount` (iMessage account), and after that make sure those handles can be added to the chat itself.

Finally, add the participant (or remove the participant). For whatever reason, the `reason` argument must be `0` or it doesn't work.

{% hint style="warning" %}
There is one caveat with adding and removing participants with this code. The participant has to be inside the iMessage `chat.db`, otherwise they may not get added. We haven't been able to find a workaround or a way of registering the participant inside the database as of yet.
{% endhint %}

### Update Chat Pinned Status

```objectivec
// if the chat is pinned, unpin it, otherwise pin it
if (!chat.isPinned) {
    // get the pinned conversation set, make it mutable, and then add the chat to be pinned
    NSArray* arr = [[[IMPinnedConversationsController sharedInstance] pinnedConversationIdentifierSet] array];
    NSMutableArray<NSString*>* chatArr = [[NSMutableArray alloc] initWithArray:(arr)];
    [chatArr addObject:(chat.pinningIdentifier)];
    // convert mutable back to immutable
    NSArray<NSString*>* finalArr = [chatArr copy];
    // update the pinned conversation array
    IMPinnedConversationsController* controller = [IMPinnedConversationsController sharedInstance];
    // contextMenu is an arbitrary value, other values may work as well
    [controller setPinnedConversationIdentifiers:(finalArr) withUpdateReason:(@"contextMenu")];
} else {
    // get the pinned conversation set, make it mutable, and then remove the chat to be unpinned
    NSArray* arr = [[[IMPinnedConversationsController sharedInstance] pinnedConversationIdentifierSet] array];
    NSMutableArray<NSString*>* chatArr = [[NSMutableArray alloc] initWithArray:(arr)];
    [chatArr removeObject:(chat.pinningIdentifier)];
    // convert mutable back to immutable
    NSArray<NSString*>* finalArr = [chatArr copy];
    // update the pinned conversation array
    IMPinnedConversationsController* controller = [IMPinnedConversationsController sharedInstance];
    // contextMenu is an arbitrary value, other values may work as well
    [controller setPinnedConversationIdentifiers:(finalArr) withUpdateReason:(@"contextMenu")];
}
```

We first grab all the pinned conversations. Then we add or remove the conversation from this array, and send it back to `IMPinnedConversationsController`. The update reason should be `contextMenu` otherwise the code may not work.

{% hint style="danger" %}
Pinning chats was introduced on Big Sur, and as such this code will crash if run on macOS 10. This code also seems to crash on Monterey and up - since we cannot run classdump on Monterey yet we have been unable to check and see if the APIs changed.

For now, only use this code on **macOS Big Sur**.
{% endhint %}

### Deleting a Chat

<pre class="language-objectivec"><code class="lang-objectivec">// Get the chat
IMChat *chat = [BlueBubblesHelper getChat: data[@"chatGuid"] :transaction];
// Use the chat registry to remove the chat
<strong>if (chat != nil) {
</strong>    [[IMChatRegistry sharedInstance] _chat_remove:(chat)];
}
</code></pre>

Pretty simple method. **This is not recoverable, the chat and its messages will be permanently deleted!**

## Messages

### Getting an IMMessage

This object is very important when sending a reply or a tapback. We need it to create the association between the existing message that is replied or "tapbacked" to, and the reply or tapback.

```objectivec
+(void) getMessageItem:(IMChat *)chat :(NSString *)actionMessageGuid completionBlock:(void (^)(IMMessage *message))block {
    [[IMChatHistoryController sharedInstance] loadMessageWithGUID:(actionMessageGuid) completionBlock:^(IMMessage *message) {
        block(message);
    }];
}
```

This is an asynchronous process in IMCore, so it requires the use of a completion block.

### Sending a Message (Including Subject, Effect, Mention, and/or Reply)

{% tabs %}
{% tab title="macOS 10 (no replies)" %}

```objectivec
// now we will deserialize the attributedBody if it exists
NSDictionary *attributedDict = data[@"attributedBody"];
// we'll create the NSMutableAttributedString with the associatedBody string if we can,
// else we'll fall back to using the message text
NSMutableAttributedString *attributedString = [[NSMutableAttributedString alloc] initWithString: data[@"message"]];
// if associateBody exists, we iterate through it
if (attributedDict != NULL && attributedDict != (NSDictionary*)[NSNull null]) {
    attributedString = [[NSMutableAttributedString alloc] initWithString: attributedDict[@"string"]];
    NSArray *attrs = attributedDict[@"runs"];
    for(NSDictionary *dict in attrs)
    {
        // construct range and attributes from dict and add to NSMutableAttributedString
        NSArray *rangeArray = dict[@"range"];
        NSRange range = NSMakeRange([(NSNumber*)[rangeArray objectAtIndex:0] intValue], [(NSNumber*)[rangeArray objectAtIndex:1] intValue]);
        NSDictionary *attrsDict = dict[@"attributes"];
        [attributedString addAttributes:attrsDict range:range];
    }
}

NSMutableAttributedString *subjectAttributedString = nil;
if (data[@"subject"] != [NSNull null] && [data[@"subject"] length] != 0) {
    subjectAttributedString = [[NSMutableAttributedString alloc] initWithString: data[@"subject"]];
}
NSString *effectId = nil;
if (data[@"effectId"] != [NSNull null] && [data[@"effectId"] length] != 0) {
    effectId = data[@"effectId"];
}

void (^createMessage)(NSAttributedString*, NSAttributedString*, NSString*, NSString*) = ^(NSAttributedString *message, NSAttributedString *subject, NSString *effectId, NSString *threadIdentifier) {
    IMMessage *messageToSend = [[IMMessage alloc] init];
    messageToSend = [messageToSend initWithSender:(nil) time:(nil) text:(message) messageSubject:(subject) fileTransferGUIDs:(nil) flags:(100005) error:(nil) guid:(nil) subject:(nil) balloonBundleID:(nil) payloadData:(nil) expressiveSendStyleID:(effectId)];
    [chat sendMessage:(messageToSend)];
    if (transaction != nil) {
        [[NetworkController sharedInstance] sendMessage: @{@"transactionId": transaction, @"identifier": [[chat lastMessage] guid]}];
    }
};

createMessage(attributedString, subjectAttributedString, effectId, nil);
```

{% endtab %}

{% tab title="macOS 11+ (replies)" %}

```objectivec
// now we will deserialize the attributedBody if it exists
NSDictionary *attributedDict = data[@"attributedBody"];
// we'll create the NSMutableAttributedString with the associatedBody string if we can,
// else we'll fall back to using the message text
NSMutableAttributedString *attributedString = [[NSMutableAttributedString alloc] initWithString: data[@"message"]];
// if associateBody exists, we iterate through it
if (attributedDict != NULL && attributedDict != (NSDictionary*)[NSNull null]) {
    attributedString = [[NSMutableAttributedString alloc] initWithString: attributedDict[@"string"]];
    NSArray *attrs = attributedDict[@"runs"];
    for(NSDictionary *dict in attrs)
    {
        // construct range and attributes from dict and add to NSMutableAttributedString
        NSArray *rangeArray = dict[@"range"];
        NSRange range = NSMakeRange([(NSNumber*)[rangeArray objectAtIndex:0] intValue], [(NSNumber*)[rangeArray objectAtIndex:1] intValue]);
        NSDictionary *attrsDict = dict[@"attributes"];
        [attributedString addAttributes:attrsDict range:range];
    }
}
// if we've got a subject, make the string
NSMutableAttributedString *subjectAttributedString = nil;
if (data[@"subject"] != [NSNull null] && [data[@"subject"] length] != 0) {
    subjectAttributedString = [[NSMutableAttributedString alloc] initWithString: data[@"subject"]];
}
// if we've got an effect ID, make the string
NSString *effectId = nil;
if (data[@"effectId"] != [NSNull null] && [data[@"effectId"] length] != 0) {
    effectId = data[@"effectId"];
}
// function we can call to create the message easily
void (^createMessage)(NSAttributedString*, NSAttributedString*, NSString*, NSString*) = ^(NSAttributedString *message, NSAttributedString *subject, NSString *effectId, NSString *threadIdentifier) {
    IMMessage *messageToSend = [[IMMessage alloc] init];
    messageToSend = [messageToSend initWithSender:(nil) time:(nil) text:(message) messageSubject:(subject) fileTransferGUIDs:(nil) flags:(100005) error:(nil) guid:(nil) subject:(nil) balloonBundleID:(nil) payloadData:(nil) expressiveSendStyleID:(effectId)];
    // remove this line of code if not on Big Sur and up!!
    messageToSend.threadIdentifier = threadIdentifier;
    [chat sendMessage:(messageToSend)];
};
// create a thread identifier if we are replying (ONLY WORKS ON BIG SUR)
if (data[@"selectedMessageGuid"] != [NSNull null] && [data[@"selectedMessageGuid"] length] != 0) {
    [BlueBubblesHelper getMessageItem:(chat) :(data[@"selectedMessageGuid"]) completionBlock:^(IMMessage *message) {
        IMMessageItem *messageItem = (IMMessageItem *)message._imMessageItem;
        NSObject *items = messageItem._newChatItems;
        IMMessagePartChatItem *item;
        // sometimes items is an array so we need to account for that
        if ([items isKindOfClass:[NSArray class]]) {
            for(IMMessagePartChatItem* imci in (NSArray *)items) {
                if([imci._item.guid isEqualToString:(data[@"selectedMessageGuid"])]) {
                    item = imci;
                }
            }
        } else {
            item = (IMMessagePartChatItem *)items;
        }
        NSString *identifier = @"";
        // either reply to an existing thread or create a new thread
        if (message.threadIdentifier != nil) {
            identifier = message.threadIdentifier;
        } else {
            identifier = IMCreateThreadIdentifierForMessagePartChatItem(item);
        }
        createMessage(attributedString, subjectAttributedString, effectId, identifier);
    }];
// otherwise just send a regular message
} else {
    createMessage(attributedString, subjectAttributedString, effectId, nil);
}`
```

{% endtab %}
{% endtabs %}

There's a lot to break down here. This serves as our all-in-one function of sending a message with an effect, subject line, mention, or reply, or any combination of these things (a message could have all of these things at once).

An example JSON object sent to the bundle might look like this:

```json
{
    "chatGuid": "iMessage;-;<chat address>",
    "subject": "Test", //message subject
    "message": "This is a reply, mention, subject, and effect message", //message text
    "attributedBody": {
        "runs": [{
            "attributes": {
                "__kIMMessagePartAttributeName": 0, //must be 0
                "__kIMMentionConfirmedMention": "<mention contact address>"
            },
            "range": [17, 7] //range is [startIndex, length] not [startIndex, endIndex]
        }],
        "string": "mention" //the string of text to show as the mention
    },
    "effectsId": "com.apple.MobileSMS.expressivesend.impact", //slam effect
    "selectedMessageGuid": "<GUID>" //guid for message being replied to
}
```

And this is how we parse it:

1. Deserialize the `attributedBody` data object. This is what contains mentions data sent from the client, specifically the range of text that is the mention, and what address it is mentioning.
2. If this data object exists, generate the message `NSAttributedString` from it. Otherwise, just make a plaintext `NSAttributedString`.
3. If we have a subject, then make a subject `NSAttributedString` (otherwise null)
4. If we have an effect ID, make an effect ID `NSString` (otherwise null)
5. **Big Sur+ Only** - If we have a selected message GUID (which means the user replied to something), generate a thread originator GUID.
   1. We first have to get the `IMMessage` object for the selected message
   2. Get an `IMMessagePartChatItem` from the `IMMessage`. We have to be very careful here as sometimes the getter returns an array, and sometimes it returns a single object.
   3. Finally, we make the thread identifier. The original message may already have the thread identifier, in which case we use that (i.e. it's already part of a thread). If it doesn't, we can use `IMCreateThreadIdentifierForMessagePartChatItem` to generate one for us.
6. Send the message with all the parameters (most can be null). Flags set to `100005` is arbitrary, that's what worked for us in our testing.

{% hint style="danger" %}
Be sure to only use the Big Sur specific methods on Big Sur, otherwise the iMessage app will crash on macOS 10.
{% endhint %}

### Parsing a Tapback into its Tapback ID

```objectivec
+(long long) parseReactionType:(NSString *)reactionType {
    NSString *lowerCaseType = [reactionType lowercaseString];

    if([@"love" isEqualToString:(lowerCaseType)]) return 2000;
    if([@"like" isEqualToString:(lowerCaseType)]) return 2001;
    if([@"dislike" isEqualToString:(lowerCaseType)]) return 2002;
    if([@"laugh" isEqualToString:(lowerCaseType)]) return 2003;
    if([@"emphasize" isEqualToString:(lowerCaseType)]) return 2004;
    if([@"question" isEqualToString:(lowerCaseType)]) return 2005;
    if([@"-love" isEqualToString:(lowerCaseType)]) return 3000;
    if([@"-like" isEqualToString:(lowerCaseType)]) return 3001;
    if([@"-dislike" isEqualToString:(lowerCaseType)]) return 3002;
    if([@"-laugh" isEqualToString:(lowerCaseType)]) return 3003;
    if([@"-emphasize" isEqualToString:(lowerCaseType)]) return 3004;
    if([@"-question" isEqualToString:(lowerCaseType)]) return 3005;
    return 0;
}
```

This function simply converts text based reaction types to their integer counterparts in iMessage.

### Sending a Tapback

{% tabs %}
{% tab title="macOS 10" %}

```objectivec
//Map the reaction type
long long reactionLong = [BlueBubblesHelper parseReactionType:(data[@"reactionType"])];
// Get the messageItem
[BlueBubblesHelper getMessageItem:(chat) :(data[@"selectedMessageGuid"]) completionBlock:^(IMMessage *message) {
    IMMessageItem *imMessage = (IMMessageItem *)message._imMessageItem;
    NSObject *items = imMessage._newChatItems;
    IMChatItem *item;
    // sometimes items is an array so we need to account for that
    if ([items isKindOfClass:[NSArray class]]) {
        for(IMChatItem* imci in (NSArray *)items) {
            if([imci._item.guid isEqualToString:(data[@"selectedMessageGuid"])]) {
                item = imci;
            }
        }
    } else {
        item = (IMChatItem *)items;
    }
    //Build the message summary
    NSDictionary *messageSummary = @{@"amc":@1,@"ams":[imMessage body].string};
    // Send the tapback
    // check if the body happens to be an object (ie an attachment) and send the tapback accordingly to show the proper summary
    NSData *dataenc = [[imMessage body].string dataUsingEncoding:NSNonLossyASCIIStringEncoding];
    NSString *encodevalue = [[NSString alloc]initWithData:dataenc encoding:NSUTF8StringEncoding];
    if ([encodevalue isEqualToString:@"\\ufffc"]) {
        [chat sendMessageAcknowledgment:(reactionLong) forChatItem:(item) withMessageSummaryInfo:(@{})];
    } else {
        [chat sendMessageAcknowledgment:(reactionLong) forChatItem:(item) withMessageSummaryInfo:(messageSummary)];
    }
}];
```

{% endtab %}

{% tab title="macOS 11+" %}
<pre class="language-objectivec"><code class="lang-objectivec">if (attributedString == nil) {
    NSString *message = data[@"message"];
    // Tapbacks will not have message text, but messages sent must have some sort of text
    if (message == nil) {
        message = @"TEMP";
    }
    attributedString = [[NSMutableAttributedString alloc] initWithString: message];
}

<strong>void (^createMessage)(NSAttributedString*, NSAttributedString*, NSString*, NSString*, NSString*, long long*, NSRange, NSDictionary*, NSArray*, BOOL) = ^(NSAttributedString *message, NSAttributedString *subject, NSString *effectId, NSString *threadIdentifier, NSString *associatedMessageGuid, long long *reaction, NSRange range, NSDictionary *summaryInfo, NSArray *transferGUIDs, BOOL isAudioMessage) {
</strong>    messageToSend = [messageToSend initWithSender:(nil) time:(nil) text:(message) messageSubject:(subject) fileTransferGUIDs:(nil) flags:(5) error:(nil) guid:(nil) subject:(nil) associatedMessageGUID:(associatedMessageGuid) associatedMessageType:*(reaction) associatedMessageRange:(range) messageSummaryInfo:(summaryInfo)];
    [chat sendMessage:(messageToSend)];
};

[BlueBubblesHelper getMessageItem:(chat) :(data[@"selectedMessageGuid"]) completionBlock:^(IMMessage *message) {
    IMMessageItem *messageItem = (IMMessageItem *)message._imMessageItem;
    NSObject *items = messageItem._newChatItems;
    IMMessagePartChatItem *item;
    // sometimes items is an array so we need to account for that
    if ([items isKindOfClass:[NSArray class]]) {
        for (IMMessagePartChatItem *i in (NSArray *) items) {
            if ([i index] == [data[@"partIndex"] integerValue]) {
                item = i;
                break;
            }
        }
    } else {
        item = (IMMessagePartChatItem *)items;
    }
    NSString *reaction = data[@"reactionType"];
    long long reactionLong = [BlueBubblesHelper parseReactionType:(reaction)];
    NSDictionary *messageSummary;
    // if we actually got an item, proceed, otherwise use a fallback
    if (item != nil) {
        NSAttributedString *text = [item text];
        if (text == nil) {
            text = [message text];
        }
        messageSummary = @{@"amc":@1,@"ams":text.string};
        // Send the tapback
        // check if the body happens to be an object (ie an attachment) and send the tapback accordingly to show the proper summary
        NSData *dataenc = [text.string dataUsingEncoding:NSNonLossyASCIIStringEncoding];
        NSString *encodevalue = [[NSString alloc]initWithData:dataenc encoding:NSUTF8StringEncoding];
        if ([encodevalue isEqualToString:@"\\ufffc"]) {
            NSMutableAttributedString *newAttributedString = [[NSMutableAttributedString alloc] initWithString: [[BlueBubblesHelper reactionToVerb:(reaction)] stringByAppendingString:(@"an attachment")]];
            createMessage(newAttributedString, subjectAttributedString, effectId, nil, [NSString stringWithFormat:@"p:%@/%@", data[@"partIndex"], [message guid]], &#x26;reactionLong, [item messagePartRange], @{}, nil, false);
        } else {
            NSMutableAttributedString *newAttributedString = [[NSMutableAttributedString alloc] initWithString: [[BlueBubblesHelper reactionToVerb:(reaction)] stringByAppendingString:([NSString stringWithFormat:(@"“%@”"), text.string])]];
            if ([item text] == nil) {
                createMessage(newAttributedString, subjectAttributedString, effectId, nil, [NSString stringWithFormat:@"bp:%@", [message guid]], &#x26;reactionLong, [item messagePartRange], messageSummary, nil, false);
            } else {
                createMessage(newAttributedString, subjectAttributedString, effectId, nil, [NSString stringWithFormat:@"p:%@/%@", data[@"partIndex"], [message guid]], &#x26;reactionLong, [item messagePartRange], messageSummary, nil, false);
            }
        }
    } else {
        messageSummary = @{@"amc":@1,@"ams":message.text.string};
        // Send the tapback
        // check if the body happens to be an object (ie an attachment) and send the tapback accordingly to show the proper summary
        NSData *dataenc = [[message text].string dataUsingEncoding:NSNonLossyASCIIStringEncoding];
        NSString *encodevalue = [[NSString alloc]initWithData:dataenc encoding:NSUTF8StringEncoding];
        NSRange range = NSMakeRange(0, [message text].string.length);
        if ([encodevalue isEqualToString:@"\\ufffc"] || [encodevalue length] == 0) {
            NSMutableAttributedString *newAttributedString = [[NSMutableAttributedString alloc] initWithString: [[BlueBubblesHelper reactionToVerb:(reaction)] stringByAppendingString:(@"an attachment")]];
            createMessage(newAttributedString, subjectAttributedString, effectId, nil, [message guid], &#x26;reactionLong, range, @{}, nil, false);
        } else {
            NSMutableAttributedString *newAttributedString = [[NSMutableAttributedString alloc] initWithString: [[BlueBubblesHelper reactionToVerb:(reaction)] stringByAppendingString:([NSString stringWithFormat:(@"“%@”"), [message text].string])]];
            createMessage(newAttributedString, subjectAttributedString, effectId, nil, [message guid], &#x26;reactionLong, range, messageSummary, nil, false);
        }
    }
}];
</code></pre>
{% endtab %}
{% endtabs %}

1. &#x20;Get the integer representation of the tapback using the `parseReactionType` function.
2. Get the `IMMessage` object for the message the user selected to react to
3. Get an `IMMessagePartChatItem` from this `IMMessage`. As is the case when sending a reply, sometimes the getter is an array and sometimes it is the single object, so watch out.
4. Build a message summary. This is what is shown on the chat list page, e.g. 'John liked "Test"'. `amc` stands for associated message content, while `ams` stands for associated message summary.&#x20;
5. Set the second value to the body of the message being reacted to. If this message is a non-text message, the body string will be the unicode `ufffc`.
6. Due to the above, we want to make sure we don't mistakenly send a summary with a non-displayable unicode character inside it. Thus, we set the summary to null if we detect the body string to be `ufffc`.
7. MacOS 11+ requires some extra things:
   1. Get the range for the message being reacted to - this refers directly with the `IMMessagePartChatItem` as a message can have multiple items and the range helps identify which one
   2. Add the partIndex to the associated message guid: `p:<index>/<guid>`
   3. Provide a fake message `NSAttributedString` - this is not actually used anywhere but avoids crashes

### Editing a Message

{% code lineNumbers="true" %}

```objectivec
// get the chat
IMChat *chat = [BlueBubblesHelper getChat: data[@"chatGuid"] :transaction];
// get the message
[BlueBubblesHelper getMessageItem:(chat) :(data[@"messageGuid"]) completionBlock:^(IMMessage *message) {
    // generate the two NSMutableAttributedStrings
    NSMutableAttributedString *editedString = [[NSMutableAttributedString alloc] initWithString: data[@"editedMessage"]];
    NSMutableAttributedString *bcString = [[NSMutableAttributedString alloc] initWithString: data[@"backwardsCompatibilityMessage"]];
    NSInteger index = data["@partIndex"];
    // send the edit
    [chat editMessage:(message) atPartIndex:(index) withNewPartText:(editedString) backwardCompatabilityText:(bcString)];
}];
```

{% endcode %}

This one is fairly easy. The only tricky part is the part index, which corresponds to which part of a message is being edited. Sometimes, a message can be stacked as so:

> Message
>
> Attachment
>
> Message

The part index tells IMCore which part of the message is actually being edited, as there are two separate message strings that could be changed. The indexes start at 0.

### Unsending a Message

{% code lineNumbers="true" %}

```objectivec
// get the chat
IMChat *chat = [BlueBubblesHelper getChat: data[@"chatGuid"] :transaction];
// get the message
[BlueBubblesHelper getMessageItem:(chat) :(data[@"messageGuid"]) completionBlock:^(IMMessage *message) {
    // find the message item corresponding to the part index
    IMMessageItem *messageItem = (IMMessageItem *)message._imMessageItem;
    NSObject *items = messageItem._newChatItems;
    IMMessagePartChatItem *item;
    // sometimes items is an array so we need to account for that
    if ([items isKindOfClass:[NSArray class]]) {
        for (IMMessagePartChatItem *i in (NSArray *) items) {
            if ([i index] == [data[@"partIndex"] integerValue]) {
                item = i;
                break;
            }
        }
    } else {
        item = (IMMessagePartChatItem *)items;
    }
    // retract (unsend) the message
    [chat retractMessagePart:(item)];
}];
```

{% endcode %}

As with editing a message, the part index is crucial to know which part has been unsent and then use the `IMMessagePartChatItem` in the method correctly.

## Currently Known Issues

1. Sometimes, the `IMMessagePartChatItem` will not exist. This is almost always for messages that are old. If this happens, tapbacks won't work, and replies will have a weird bug where they attach to an "empty" message. See this [ticket](https://github.com/BlueBubblesApp/bluebubbles-helper/issues/11) for more details.
2. The reaction function currently doesn't support reacting to a message with multiple parts, for example a message with multiple attachments. This is likely also to do with `IMMessagePartChatItem` but we haven't figured it out quite yet. See this [ticket](https://github.com/BlueBubblesApp/bluebubbles-helper/issues/8) for more details.
