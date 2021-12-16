


# Attachment MiddleWare (Adaptive Card)

In the NPS flow, the user can be presented with an Adaptive Card asking them to select their feedback score from a list of options presented as buttons.

The default behaviour will leave those buttons as clickable after the user has selected one. Although this won't break the bot, to have the card disabled after they select their answer you can pass the following middleware component to the `<ReactWebChat />` component.

```
      const attachmentMiddleWares = () => (next:any) => ({activity, attachment, ...others}:any) => {
        const { activities } = store.getState();
        const messageActivities = activities.filter((activity:any) => activity.type === 'message');
        const recentBotMessage = messageActivities.pop() === activity;

        switch (attachment.contentType) {
            case 'application/vnd.microsoft.card.adaptive':
              return (
                <AdaptiveCardContent
                  actionPerformedClassName="card__action--performed"
                  content={attachment.content}
                  disabled={!recentBotMessage}
                />
              );

            default:
              return next({activity, attachment, ...others});
        }

      }
  ```

```
              <ReactWebChat
                attachmentMiddleware={attachmentMiddleWares}
                store={store}
                directLine={directLine}
            />
   ```
