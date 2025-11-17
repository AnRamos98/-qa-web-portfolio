**Table of Contents:**


- [What exaclty is Postman??](#what-exaclty-is-postman)
- [Install Postman first](#install-postman-first)
- [API Example Setup](#api-example-setup)
  - [Create Your Trello Account](#create-your-trello-account)
  - [Trello's API](#trellos-api)
  - [Trello's Power Up, API Key and Token](#trellos-power-up-api-key-and-token)
- [Roll up our sleeves](#roll-up-our-sleeves)
  - [Create a List](#create-a-list)
  - [Create a Card](#create-a-card)
  - [Updating a Card](#updating-a-card)
  - [Delete a Card](#delete-a-card)
  - [Delete a Board](#delete-a-board)

## What exaclty is Postman??
Postman is an all-in-one API platform used not only by developers, but also by testers. It helps us build, test, and manage APIs in a simple and intuitive way.
Sounds good, right?

This amazing tool streamlines the entire API lifecycle by providing a clean, user-friendly interface where you can:

👉 Send and inspect HTTP requests

👉 Test API behavior using automated scripts

👉 Organize and document your work

👉 Collaborate easily with your team

👉 Monitor API performance over time

In short: Postman makes working with APIs easier, faster, and much more pleasant,  whether you're a beginner or an experienced QA.

Learning how to use a new platform doesn’t have to be a daunting task.

Sure, sometimes it feels overwhelming at first, but when we break things down and explain them in a simple, practical way, everything becomes much easier. 
And the best part? The knowledge sticks.

So, with that spirit in mind, let’s walk through how to use Postman, step by step, in a friendly and straightforward way.


## Install Postman first

Before we start, we ’ll need to install Postman (of course!! otherwise we wouldn’t be able to do anything, and we wouldn’t be able to get our hands dirty like we love! 😄)

Just type “Postman” into Google’s search bar and click on the first link that appears, or go directly to their official website: [Postman](https://www.postman.com/)

Once you're there, choose your operating system, select the correct system type, and the installer (.exe, .dmg, etc.) will start downloading automatically.

After the installer has finished downloading, run it and follow the installation steps.
You can safely go with the default settings, nothing complicated here.

And… tcha-naaaam!! 🎉🎉

Postman is installed, and you’re now ready to explore this powerful tool, test APIs, and hunt down even the most mysterious bugs.


## API Example Setup

### Create Your Trello Account

To show you how to use Postman, I’m going to use the Trello REST APIs as an example.
Of course, you can use any other API you prefer, the way we use Postman will be exactly (or almost) the same.


Before we start playing with Trello’s REST API in Postman, you’ll need to have your own Trello account.

No account → no boards → no API access. Simple as that.

Here’s what to do:
1. Go to [Trello](https://trello.com/)
2. Click on “Get Trello for free”
3. Enter your email address
4. Click Sign Up
5. Follow the usual steps you’d take when creating an account and choose the options that best fit your needs (Don’t Forget to verify your email!!!!!)

This is what you’ll see once you verify your email and log in to your Trello account:

![My image](./postman_trello_howto_images/trello's_first_page.png)

To create a new board, cards, or anything else in Trello, you can do it manually, or you can do it through Trello’s APIs almost automatically.
And this is where Postman comes in. It allows you to do all of this in a simple and easy way, while also helping you understand how APIs work and how to test them effectively.
To learn how to do this, just check the [Roll up our sleeves section](#roll-up-our-sleeves)


### Trello's API

To learn how Trello’s APIs work, just type “Trello API” into Google’s search bar and click on the first link that appears, or go directly to their official documentation [Trello's API](https://developer.atlassian.com/cloud/trello/rest/).

Here you’ll find an excellent manual explaining how [Trello's API](https://developer.atlassian.com/cloud/trello/rest/) works.
On the left-hand menu, you’ll see all the available features and their corresponding API methods (GET, PUT, POST, DELETE, etc.) that you can use through Postman.


### Trello's Power Up, API Key and Token

Before we start making any calls, we need to create a Trello's Power-Up and then generate our API key and token.
To create both, just follow the instructions in the [official guide](https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/).


## Roll up our sleeves

We can now finally start using Postman. As mentioned earlier, we will be working with Trello’s API, and the first request we will make is to create a board.
To do this, we need to check Trello’s API documentation to understand how the request should be made.
Go to the [Trello's API](https://developer.atlassian.com/cloud/trello/rest/), and in the menu on the left, look for “Board” and then click on “Create a Board”:

![My image](./postman_trello_howto_images/create_a_board_left_menu.png)

To create a board, we must use the **POST** method, as specified in the API documentation. This endpoint includes required and optional parameters, all listed in the guide:

![My image](./postman_trello_howto_images/create_a_board_method.png)

For this request, we use the code example provided under “Create a Board”. The only part we need is the URL shown on the second line:

![My image](./postman_trello_howto_images/create_a_board_url.png)

In that URL, you can see the `APIkey`, the `APItoken`, and the required name parameter:

![My image](./postman_trello_howto_images/url.png)

After pasting this URL into Postman, all the parameters will appear automatically, ready for you to fill in with your own values:

![My image](./postman_trello_howto_images/postman_past_url.png)

After you fill in all the necessary parameters, you can send the request to create a new board by clicking the Send button:

![My image](./postman_trello_howto_images/send_button.png)

Once we click the Send button, we will receive a response from Trello’s API indicating that the request was completed successfully:

![My image](./postman_trello_howto_images/API_answer.png)

If we open the Trello app, we can verify that a new board with the specified name has been created successfully. Impressive, isn’t it?

![My image](./postman_trello_howto_images/trello_board_created.png)

Now we can test other requests like creating a list, creating a card, updating a card, deleting a card, and even deleting a whole board! Let’s do it and have a little fun with Postman!

### Create a List

To create a list, we need to use the **POST** method. The `name` and `idBoard` are required parameters, and of course we also need the `APIkey` and `APItoken` (even though they don’t appear in the required-parameters list, they do show up in the URL info). So let’s throw all of this into Postman and see what happens!

![My image](./postman_trello_howto_images/create_a_list.png)

Just copy and paste the URL provided in Postman: 

![My image](./postman_trello_howto_images/postman_create_a_list.png)

Don't forget that the `idBoard` is the one you got when you create a new board you can find it here:

![My image](./postman_trello_howto_images/id_board.png)

Fill the corresponding fields:

![My image](./postman_trello_howto_images/postman_fill_fields.png)

Click the Send button and verify that the API response was successful:

![My image](./postman_trello_howto_images/create_a_list_successfully_response.png)
 
And check whether the list with the given name (List_name) appears on the Trello board:

![My image](./postman_trello_howto_images/Trello_list.png)


### Create a Card

To create a card, we need to use the **POST** method. The `idList` is a required parameter, and of course we also need the `CardName` (even though it’s not marked as required), plus the `APIkey` and `APItoken` (they don’t appear in the required parameters list, but they do show up in the URL info). So let’s toss all of this into Postman and see what happens!

![My image](./postman_trello_howto_images/create_a_card.png)

Just copy and paste the URL provided in Postman: 

![My image](./postman_trello_howto_images/postman_create_a_card.png)

As mentioned, we’ll need to add a name for the card. Even though it’s not a required field, it helps us keep things organized and actually know what we just created. As you can see, the card name isn’t included in the URL, so we’ll add it manually by appending `&name=CardName` to the end of the URL:

![My image](./postman_trello_howto_images/adding_card_name_url.png)


Don't forget that the `idList` is the one you got when you create a new list you can find it here:

![My image](./postman_trello_howto_images/id_list.png)

Fill the corresponding fields:

![My image](./postman_trello_howto_images/postman_fill_fields_card.png)

Click the Send button and verify that the API response was successful:

![My image](./postman_trello_howto_images/create_a_card_successfully_response.png)
 
And check whether the card with the given name (Card_name) appears on the Trello board:

![My image](./postman_trello_howto_images/Trello_card.png)


### Updating a Card

To update a card, we need to use the **PUT** method. The `id` is a required parameter, and we also need to add `&name=CardName` (even though it’s not marked as required but if we’re updating the card, we definitely need it), plus the `APIKey` and `APIToken` (they don’t appear in the required-parameters list, but they do show up in the URL info). So let’s toss all of this into Postman and see what happens!

![My image](./postman_trello_howto_images/update_a_card.png)

Just copy and paste the URL provided in Postman: 

![My image](./postman_trello_howto_images/postman_updating_a_card.png)

As mentioned, the `id` is a required parameter, and we also need to add `&name=CardName`. As we can see, the URL already contains the `id`, but it’s inside {}. We just need to remove the braces and add a : before the `id` so Postman recognizes it as a variable. As for `&name=CardName`, we’ll add it manually by appending it to the end of the URL: 

![My image](./postman_trello_howto_images/adding_id_url.png)

The `id` is the `idCard` we got when we create a new card and you can find it here:

![My image](./postman_trello_howto_images/id_card.png)

Fill the corresponding fields:

![My image](./postman_trello_howto_images/postman_fill_fields_updating_card.png)

If you notice, we changed the card name to a different one. That’s the whole purpose of this request, updating the card’s name—so we need to give it a new name to verify that everything is working properly.

Click the Send button and verify that the API response was successful:

![My image](./postman_trello_howto_images/updating_a_card_successfully_response.png)
 
And check whether the card with the given name (Card_name_Updated) appears on the Trello board:

![My image](./postman_trello_howto_images/Trello_updating_card.png)

### Delete a Card

To delete a card, we need to use the **DEL** method. The `id` is a required parameter, plus the `APIKey` and `APIToken` (they don’t appear in the required-parameters list, but they do show up in the URL info). So let’s toss all of this into Postman and see what happens!

![My image](./postman_trello_howto_images/delete_a_card.png)

Just copy and paste the URL provided in Postman: 

![My image](./postman_trello_howto_images/postman_delete_a_card.png)

As mentioned, the `id` is a required parameter and the URL already contains the `id`, but it’s inside {}. We just need to remove the braces and add a : before the `id` so Postman recognizes it as a variable. 

![My image](./postman_trello_howto_images/adding_id_url_delete.png)

The `id` is the `idCard` we got when we create a new card and you can find it here:

![My image](./postman_trello_howto_images/id_card.png)

Fill the corresponding fields:

![My image](./postman_trello_howto_images/postman_fill_fields_delete_card.png)


Click the Send button and verify that the API response was successful:

![My image](./postman_trello_howto_images/delete_a_card_successfully_response.png)
 
And check whether the card with the given name (Card_name_Updated) appears on the Trello board:

![My image](./postman_trello_howto_images/Trello_deleted_card.png)

### Delete a Board








