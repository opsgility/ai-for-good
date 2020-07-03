<style>
figcaption{
    display: none !important;
}
</style>

## Exercise 5: Test the bot with Teams

Once deployed, bots can be connected to channels such as Slack, Microsoft Teams, and Facebook Messenger, where you can interact with them the way you would with a person. In this exercise, you will add the bot to your Teams chat and carry on a conversation with it in Teams.

1. You can download and install Teams from here and start a free trial:

    ```html
    http://teams.microsoft.com/
    ```

2. Return to your Web App Bot in the Azure Portal and click **Channels** in the menu on the left. Click the **Teams** icon. Then click **Save** at the bottom of the blade.

    ![Editing the Teams channel](Images/19-11-06-teamschannel.png)

    _Editing the Teams channel_

3. Click into another section and click into **Channels**, then click **Teams**. Then click **Open Microsoft Teams** to add the bot to Teams and launch Teams.

    ![Connecting to Teams](Images/19-11-06-OpenTeams.png)

    _Connecting to Skype_

4. Start a conversation with by typing "hi" into the Teams window. Then converse with the bot by asking it questions and seeing how it responds. Refer to the **Factbot.tsv** file that you used to populate the knowledge base in [Exercise 2](#Exercise2) for examples of questions to ask.

    ![Chatting with the bot in Teams](Images/19-11-06-TeamsChat.png)

    _Chatting with the bot in Teams_

You now have a fully functional bot created with the Azure Bot Service, infused with intelligence by Microsoft QnA Maker, and available for anyone in the world to interact with. Feel free to plug your bot into other channels and test it in different scenarios. And if you would like to make the bot smarter, consider expanding the knowledge base with additional questions and answers. For example, you could use the [online FAQ](https://docs.microsoft.com/azure/bot-service/bot-service-resources-bot-framework-faq?view=azure-bot-service-3.0) for the Bot Framework to train the bot to answer questions about the framework itself.