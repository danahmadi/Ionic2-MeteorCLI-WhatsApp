[{]: <region> (header)
# Step 4: Messages page
[}]: #
[{]: <region> (body)
In this step we will add the messages view and the ability to send messages.

Before we implement anything related to the messages pages, we first have to make sure that once we click on a chat item in the chats page, we will be promoted into its corresponding messages view.

Let's first implement the `showMessages()` method in the chats component

[{]: <helper> (diff_step 4.1)
#### Step 4.1: Added showMessage method

##### Changed client/imports/pages/chats/chats.component.ts
```diff
@@ -7,6 +7,7 @@
 ┊ 7┊ 7┊import {Chats} from "../../../../both/collections/chats.collection";
 ┊ 8┊ 8┊import {Message} from "../../../../both/models/message.model";
 ┊ 9┊ 9┊import {Messages} from "../../../../both/collections/messages.collection";
+┊  ┊10┊import {NavController} from "ionic-angular";
 ┊10┊11┊
 ┊11┊12┊@Component({
 ┊12┊13┊  selector: "chats",
```
```diff
@@ -18,7 +19,7 @@
 ┊18┊19┊export class ChatsComponent implements OnInit {
 ┊19┊20┊  chats: Observable<Chat[]>;
 ┊20┊21┊
-┊21┊  ┊  constructor() {
+┊  ┊22┊  constructor(private navCtrl: NavController) {
 ┊22┊23┊
 ┊23┊24┊  }
 ┊24┊25┊
```
```diff
@@ -40,4 +41,8 @@
 ┊40┊41┊        )
 ┊41┊42┊      ).zone();
 ┊42┊43┊  }
+┊  ┊44┊
+┊  ┊45┊  showMessages(chat): void {
+┊  ┊46┊    this.navCtrl.push(MessagesPage, {chat});
+┊  ┊47┊  }
 ┊43┊48┊}
```
[}]: #

And let's register the click event in the view:

[{]: <helper> (diff_step 4.2)
#### Step 4.2: Added the action to the button

##### Changed client/imports/pages/chats/chats.component.html
```diff
@@ -11,7 +11,7 @@
 ┊11┊11┊
 ┊12┊12┊<ion-content class="chats-page-content">
 ┊13┊13┊  <ion-list class="chats">
-┊14┊  ┊    <button ion-item *ngFor="let chat of chats | async" class="chat">
+┊  ┊14┊    <button ion-item *ngFor="let chat of chats | async" class="chat" (click)="showMessages(chat)">
 ┊15┊15┊      <img class="chat-picture" [src]="chat.picture">
 ┊16┊16┊
 ┊17┊17┊      <div class="chat-info">
```
[}]: #

Notice how we used we used a controller called `NavController`. The NavController is `Ionic`'s new method to navigate in our app, we can also use a traditional router, but since in a mobile app we have no access to the url bar, this might come more in handy. You can read more about the NavController [here](http://ionicframework.com/docs/v2/api/components/nav/NavController/).

Let's go ahead and implement the messages component. We'll call it `MessagesPage`:

[{]: <helper> (diff_step 4.3)
#### Step 4.3: Create a stub for the component

##### Added client/imports/pages/chat/messages-page.component.ts
```diff
@@ -0,0 +1,21 @@
+┊  ┊ 1┊import {Component, OnInit} from "@angular/core";
+┊  ┊ 2┊import {NavParams} from "ionic-angular";
+┊  ┊ 3┊import {Chat} from "../../../../both/models/chat.model";
+┊  ┊ 4┊
+┊  ┊ 5┊@Component({
+┊  ┊ 6┊  selector: "messages-page",
+┊  ┊ 7┊  template: `Messages Page`
+┊  ┊ 8┊})
+┊  ┊ 9┊export class MessagesPage implements OnInit {
+┊  ┊10┊  private selectedChat: Chat;
+┊  ┊11┊
+┊  ┊12┊  constructor(navParams: NavParams) {
+┊  ┊13┊    this.selectedChat = <Chat>navParams.get('chat');
+┊  ┊14┊
+┊  ┊15┊    console.log("Selected chat is: ", this.selectedChat);
+┊  ┊16┊  }
+┊  ┊17┊
+┊  ┊18┊  ngOnInit() {
+┊  ┊19┊
+┊  ┊20┊  }
+┊  ┊21┊}🚫↵
```
[}]: #

As you can see, in order to get the chat's id we used the `NavParams` service. This is a simple service which gives you access to a key-value storage containing all the parameters we've passed using the NavController. For more information about the NavParams service, see the following [link](http://ionicframework.com/docs/v2/api/components/nav/NavParams).

Now it has to be added to AppModule:

[{]: <helper> (diff_step 4.4)
#### Step 4.4: Added the Component to the NgModule

##### Changed client/imports/app/app.module.ts
```diff
@@ -4,19 +4,22 @@
 ┊ 4┊ 4┊import {TabsContainerComponent} from "../pages/tabs-container/tabs-container.component";
 ┊ 5┊ 5┊import {ChatsComponent} from "../pages/chats/chats.component";
 ┊ 6┊ 6┊import {MomentModule} from "angular2-moment";
+┊  ┊ 7┊import {MessagesPage} from "../pages/chat/messages-page.component";
 ┊ 7┊ 8┊
 ┊ 8┊ 9┊@NgModule({
 ┊ 9┊10┊  // Components, Pipes, Directive
 ┊10┊11┊  declarations: [
 ┊11┊12┊    AppComponent,
 ┊12┊13┊    TabsContainerComponent,
-┊13┊  ┊    ChatsComponent
+┊  ┊14┊    ChatsComponent,
+┊  ┊15┊    MessagesPage
 ┊14┊16┊  ],
 ┊15┊17┊  // Entry Components
 ┊16┊18┊  entryComponents: [
 ┊17┊19┊    AppComponent,
 ┊18┊20┊    TabsContainerComponent,
-┊19┊  ┊    ChatsComponent
+┊  ┊21┊    ChatsComponent,
+┊  ┊22┊    MessagesPage
 ┊20┊23┊  ],
 ┊21┊24┊  // Providers
 ┊22┊25┊  providers: [
```
[}]: #

We've used `MessagesPage` in `ChatsComponent` but we haven't imported it yet, let's make it now:

[{]: <helper> (diff_step 4.5)
#### Step 4.5: Added the correct import

##### Changed client/imports/pages/chats/chats.component.ts
```diff
@@ -8,6 +8,7 @@
 ┊ 8┊ 8┊import {Message} from "../../../../both/models/message.model";
 ┊ 9┊ 9┊import {Messages} from "../../../../both/collections/messages.collection";
 ┊10┊10┊import {NavController} from "ionic-angular";
+┊  ┊11┊import {MessagesPage} from "../chat/messages-page.component";
 ┊11┊12┊
 ┊12┊13┊@Component({
 ┊13┊14┊  selector: "chats",
```
[}]: #

Now we can add some data to the component. We need a title and a picture to use inside the chat window. 
We also need a message:

[{]: <helper> (diff_step 4.6)
#### Step 4.6: Add basic messages component

##### Changed client/imports/pages/chat/messages-page.component.ts
```diff
@@ -1,21 +1,40 @@
 ┊ 1┊ 1┊import {Component, OnInit} from "@angular/core";
 ┊ 2┊ 2┊import {NavParams} from "ionic-angular";
 ┊ 3┊ 3┊import {Chat} from "../../../../both/models/chat.model";
+┊  ┊ 4┊import {Messages} from "../../../../both/collections/messages.collection";
+┊  ┊ 5┊import {Observable} from "rxjs";
+┊  ┊ 6┊import {Message} from "../../../../both/models/message.model";
+┊  ┊ 7┊import template from "./messages-page.component.html";
 ┊ 4┊ 8┊
 ┊ 5┊ 9┊@Component({
 ┊ 6┊10┊  selector: "messages-page",
-┊ 7┊  ┊  template: `Messages Page`
+┊  ┊11┊  template
 ┊ 8┊12┊})
 ┊ 9┊13┊export class MessagesPage implements OnInit {
 ┊10┊14┊  private selectedChat: Chat;
+┊  ┊15┊  private title: string;
+┊  ┊16┊  private picture: string;
+┊  ┊17┊  private messages: Observable<Message[]>;
 ┊11┊18┊
 ┊12┊19┊  constructor(navParams: NavParams) {
 ┊13┊20┊    this.selectedChat = <Chat>navParams.get('chat');
-┊14┊  ┊
-┊15┊  ┊    console.log("Selected chat is: ", this.selectedChat);
+┊  ┊21┊    this.title = this.selectedChat.title;
+┊  ┊22┊    this.picture = this.selectedChat.picture;
 ┊16┊23┊  }
 ┊17┊24┊
 ┊18┊25┊  ngOnInit() {
+┊  ┊26┊    let isEven = false;
+┊  ┊27┊
+┊  ┊28┊    this.messages = Messages.find(
+┊  ┊29┊      {chatId: this.selectedChat._id},
+┊  ┊30┊      {sort: {createdAt: 1}}
+┊  ┊31┊    ).map((messages: Message[]) => {
+┊  ┊32┊      messages.forEach((message: Message) => {
+┊  ┊33┊        message.ownership = isEven ? 'mine' : 'other';
+┊  ┊34┊        isEven = !isEven;
+┊  ┊35┊      });
 ┊19┊36┊
+┊  ┊37┊      return messages;
+┊  ┊38┊    });
 ┊20┊39┊  }
 ┊21┊40┊}🚫↵
```
[}]: #

As you probably noticed, we added the ownership for each message. 
We're not able to determine the author of a message so we mark every even message as ours.

Let's add the `ownership` property to the model:

[{]: <helper> (diff_step 4.7)
#### Step 4.7: Add 'ownership' property to message model

##### Changed both/models/message.model.ts
```diff
@@ -2,5 +2,6 @@
 ┊2┊2┊  _id?: string;
 ┊3┊3┊  chatId?: string;
 ┊4┊4┊  content?: string;
+┊ ┊5┊  ownership?: string;
 ┊5┊6┊  createdAt?: Date;
 ┊6┊7┊}🚫↵
```
[}]: #

One thing missing, the template:

[{]: <helper> (diff_step 4.8)
#### Step 4.8: Add basic messages view template

##### Added client/imports/pages/chat/messages-page.component.html
```diff
@@ -0,0 +1,25 @@
+┊  ┊ 1┊<ion-header>
+┊  ┊ 2┊  <ion-navbar color="whatsapp" class="messages-page-navbar">
+┊  ┊ 3┊    <ion-buttons>
+┊  ┊ 4┊      <img class="chat-picture" [src]="picture">
+┊  ┊ 5┊    </ion-buttons>
+┊  ┊ 6┊
+┊  ┊ 7┊    <ion-title class="chat-title">{{title}}</ion-title>
+┊  ┊ 8┊
+┊  ┊ 9┊    <ion-buttons end>
+┊  ┊10┊      <button ion-button icon-only class="attach-button"><ion-icon name="attach"></ion-icon></button>
+┊  ┊11┊      <button ion-button icon-only class="settings-button"><ion-icon name="more"></ion-icon></button>
+┊  ┊12┊    </ion-buttons>
+┊  ┊13┊  </ion-navbar>
+┊  ┊14┊</ion-header>
+┊  ┊15┊
+┊  ┊16┊<ion-content padding class="messages-page-content">
+┊  ┊17┊  <ion-scroll scrollY="true" class="messages">
+┊  ┊18┊    <div *ngFor="let message of messages | async" class="message-wrapper">
+┊  ┊19┊      <div [class]="'message message-' + message.ownership">
+┊  ┊20┊        <div class="message-content">{{message.content}}</div>
+┊  ┊21┊        <span class="message-timestamp">{{message.createdAt}}</span>
+┊  ┊22┊      </div>
+┊  ┊23┊    </div>
+┊  ┊24┊  </ion-scroll>
+┊  ┊25┊</ion-content>🚫↵
```
[}]: #

The template has a picture and a title inside the Navigation Bar. 
It has also two buttons. Purpose of the first one is to send an attachement. The second one, just like in Chats, is to show more options.
As the content, we used list of messages.

It doesn't look quite good as it should, let's add some style:

[{]: <helper> (diff_step 4.9)
#### Step 4.9: Add basic messages stylesheet

##### Added client/imports/pages/chat/messages-page.component.scss
```diff
@@ -0,0 +1,93 @@
+┊  ┊ 1┊.messages-page-navbar {
+┊  ┊ 2┊  .chat-picture {
+┊  ┊ 3┊    width: 50px;
+┊  ┊ 4┊    border-radius: 50%;
+┊  ┊ 5┊    float: left;
+┊  ┊ 6┊  }
+┊  ┊ 7┊
+┊  ┊ 8┊  .chat-title {
+┊  ┊ 9┊    line-height: 50px;
+┊  ┊10┊    float: left;
+┊  ┊11┊  }
+┊  ┊12┊}
+┊  ┊13┊
+┊  ┊14┊.messages-page-content {
+┊  ┊15┊  > .scroll-content {
+┊  ┊16┊    margin: 42px -15px 53px !important;
+┊  ┊17┊  }
+┊  ┊18┊
+┊  ┊19┊  .messages {
+┊  ┊20┊    height: 100%;
+┊  ┊21┊    background-image: url(/assets/chat-background.jpg);
+┊  ┊22┊    background-color: #E0DAD6;
+┊  ┊23┊    background-repeat: no-repeat;
+┊  ┊24┊    background-size: cover;
+┊  ┊25┊  }
+┊  ┊26┊
+┊  ┊27┊  .message-wrapper {
+┊  ┊28┊    margin-bottom: 9px;
+┊  ┊29┊
+┊  ┊30┊    &::after {
+┊  ┊31┊      content: "";
+┊  ┊32┊      display: table;
+┊  ┊33┊      clear: both;
+┊  ┊34┊    }
+┊  ┊35┊  }
+┊  ┊36┊
+┊  ┊37┊  .message {
+┊  ┊38┊    display: inline-block;
+┊  ┊39┊    position: relative;
+┊  ┊40┊    max-width: 236px;
+┊  ┊41┊    border-radius: 7px;
+┊  ┊42┊    box-shadow: 0 1px 2px rgba(0, 0, 0, .15);
+┊  ┊43┊
+┊  ┊44┊    &.message-mine {
+┊  ┊45┊      float: right;
+┊  ┊46┊      background-color: #DCF8C6;
+┊  ┊47┊
+┊  ┊48┊      &::before {
+┊  ┊49┊        right: -11px;
+┊  ┊50┊        background-image: url(/assets/message-mine.png)
+┊  ┊51┊      }
+┊  ┊52┊    }
+┊  ┊53┊
+┊  ┊54┊    &.message-other {
+┊  ┊55┊      float: left;
+┊  ┊56┊      background-color: #FFF;
+┊  ┊57┊
+┊  ┊58┊      &::before {
+┊  ┊59┊        left: -11px;
+┊  ┊60┊        background-image: url(/assets/message-other.png)
+┊  ┊61┊      }
+┊  ┊62┊    }
+┊  ┊63┊
+┊  ┊64┊    &.message-other::before, &.message-mine::before {
+┊  ┊65┊      content: "";
+┊  ┊66┊      position: absolute;
+┊  ┊67┊      bottom: 3px;
+┊  ┊68┊      width: 12px;
+┊  ┊69┊      height: 19px;
+┊  ┊70┊      background-position: 50% 50%;
+┊  ┊71┊      background-repeat: no-repeat;
+┊  ┊72┊      background-size: contain;
+┊  ┊73┊    }
+┊  ┊74┊
+┊  ┊75┊    .message-content {
+┊  ┊76┊      padding: 5px 7px;
+┊  ┊77┊      word-wrap: break-word;
+┊  ┊78┊
+┊  ┊79┊      &::after {
+┊  ┊80┊        content: " \00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0\00a0";
+┊  ┊81┊        display: inline;
+┊  ┊82┊      }
+┊  ┊83┊    }
+┊  ┊84┊
+┊  ┊85┊    .message-timestamp {
+┊  ┊86┊      position: absolute;
+┊  ┊87┊      bottom: 2px;
+┊  ┊88┊      right: 7px;
+┊  ┊89┊      font-size: 12px;
+┊  ┊90┊      color: gray;
+┊  ┊91┊    }
+┊  ┊92┊  }
+┊  ┊93┊}🚫↵
```
[}]: #

Now we can add it to the component:

[{]: <helper> (diff_step 4.10)
#### Step 4.10: Load the stylesheet into the Component

##### Changed client/imports/pages/chat/messages-page.component.ts
```diff
@@ -5,10 +5,14 @@
 ┊ 5┊ 5┊import {Observable} from "rxjs";
 ┊ 6┊ 6┊import {Message} from "../../../../both/models/message.model";
 ┊ 7┊ 7┊import template from "./messages-page.component.html";
+┊  ┊ 8┊import style from "./messages-page.component.scss";
 ┊ 8┊ 9┊
 ┊ 9┊10┊@Component({
 ┊10┊11┊  selector: "messages-page",
-┊11┊  ┊  template
+┊  ┊12┊  template,
+┊  ┊13┊  styles: [
+┊  ┊14┊    style
+┊  ┊15┊  ]
 ┊12┊16┊})
 ┊13┊17┊export class MessagesPage implements OnInit {
 ┊14┊18┊  private selectedChat: Chat;
```
[}]: #

This style requires us to add some assets, first we will create a copy called `assets` inside a `public` directory and then we will copy them like so:

    $ mkdir public/assets
    $ cd public/assets
    $ wget https://github.com/Urigo/Ionic2-MeteorCLI-WhatsApp/blob/master/www/assets/chat-background.jpg
    $ wget https://github.com/Urigo/Ionic2-MeteorCLI-WhatsApp/blob/master/www/assets/message-mine.jpg
    $ wget https://github.com/Urigo/Ionic2-MeteorCLI-WhatsApp/blob/master/www/assets/message-other.jpg

Now we need to take care of the message's timestamp and format it, then again we gonna use `angular2-moment` only this time we gonna use a different format using the `AmDateFormat` pipe:

[{]: <helper> (diff_step 4.12)
#### Step 4.12: Apply date format pipe to messages view template

##### Changed client/imports/pages/chat/messages-page.component.html
```diff
@@ -18,7 +18,7 @@
 ┊18┊18┊    <div *ngFor="let message of messages | async" class="message-wrapper">
 ┊19┊19┊      <div [class]="'message message-' + message.ownership">
 ┊20┊20┊        <div class="message-content">{{message.content}}</div>
-┊21┊  ┊        <span class="message-timestamp">{{message.createdAt}}</span>
+┊  ┊21┊        <span class="message-timestamp">{{message.createdAt | amDateFormat: 'HH:MM'}}</span>
 ┊22┊22┊      </div>
 ┊23┊23┊    </div>
 ┊24┊24┊  </ion-scroll>
```
[}]: #


Our messages are set, but there is one really important feature missing and that's sending messages. Let's implement our message editor.

We will start with the view itself. We will add an input for editing our messages, a `send` button, and a `record` button whos logic won't be implemented in this tutorial since we only wanna focus on the text messaging system. To fulfill this layout we gonna use a tool-bar (`ion-toolbar`) inside a footer (`ion-footer`) and place it underneath the content of the view:

[{]: <helper> (diff_step 4.13)
#### Step 4.13: Add message editor to messages view template

##### Changed client/imports/pages/chat/messages-page.component.html
```diff
@@ -22,4 +22,20 @@
 ┊22┊22┊      </div>
 ┊23┊23┊    </div>
 ┊24┊24┊  </ion-scroll>
-┊25┊  ┊</ion-content>🚫↵
+┊  ┊25┊</ion-content>
+┊  ┊26┊
+┊  ┊27┊<ion-footer>
+┊  ┊28┊  <ion-toolbar whatsapp class="messages-page-footer" position="bottom">
+┊  ┊29┊    <ion-input [(ngModel)]="message" (keypress)="onInputKeypress($event)" class="message-editor" placeholder="Type a message"></ion-input>
+┊  ┊30┊
+┊  ┊31┊    <ion-buttons end>
+┊  ┊32┊      <button ion-button icon-only *ngIf="message" class="message-editor-button" (click)="sendMessage()">
+┊  ┊33┊        <ion-icon name="send"></ion-icon>
+┊  ┊34┊      </button>
+┊  ┊35┊
+┊  ┊36┊      <button ion-button icon-only *ngIf="!message" class="message-editor-button">
+┊  ┊37┊        <ion-icon name="mic"></ion-icon>
+┊  ┊38┊      </button>
+┊  ┊39┊    </ion-buttons>
+┊  ┊40┊  </ion-toolbar>
+┊  ┊41┊</ion-footer>🚫↵
```
[}]: #

Our stylesheet requires few adjustments as well:

[{]: <helper> (diff_step 4.14)
#### Step 4.14: Add message-editor style to messages stylesheet

##### Changed client/imports/pages/chat/messages-page.component.scss
```diff
@@ -90,4 +90,23 @@
 ┊ 90┊ 90┊      color: gray;
 ┊ 91┊ 91┊    }
 ┊ 92┊ 92┊  }
+┊   ┊ 93┊}
+┊   ┊ 94┊
+┊   ┊ 95┊.messages-page-footer {
+┊   ┊ 96┊  padding-right: 0;
+┊   ┊ 97┊
+┊   ┊ 98┊  .message-editor {
+┊   ┊ 99┊    margin-left: 2px;
+┊   ┊100┊    padding-left: 5px;
+┊   ┊101┊    background: white;
+┊   ┊102┊    border-radius: 3px;
+┊   ┊103┊  }
+┊   ┊104┊
+┊   ┊105┊  .message-editor-button {
+┊   ┊106┊    box-shadow: none;
+┊   ┊107┊    width: 50px;
+┊   ┊108┊    height: 50px;
+┊   ┊109┊    font-size: 17px;
+┊   ┊110┊    margin: auto;
+┊   ┊111┊  }
 ┊ 93┊112┊}🚫↵
```
[}]: #

Now we can add handle message sending inside the component:

[{]: <helper> (diff_step 4.15)
#### Step 4.15: Add 'sendMessage()' handler to messages component

##### Changed client/imports/pages/chat/messages-page.component.ts
```diff
@@ -6,6 +6,7 @@
 ┊ 6┊ 6┊import {Message} from "../../../../both/models/message.model";
 ┊ 7┊ 7┊import template from "./messages-page.component.html";
 ┊ 8┊ 8┊import style from "./messages-page.component.scss";
+┊  ┊ 9┊import {MeteorObservable} from "meteor-rxjs";
 ┊ 9┊10┊
 ┊10┊11┊@Component({
 ┊11┊12┊  selector: "messages-page",
```
```diff
@@ -19,6 +20,7 @@
 ┊19┊20┊  private title: string;
 ┊20┊21┊  private picture: string;
 ┊21┊22┊  private messages: Observable<Message[]>;
+┊  ┊23┊  private message = "";
 ┊22┊24┊
 ┊23┊25┊  constructor(navParams: NavParams) {
 ┊24┊26┊    this.selectedChat = <Chat>navParams.get('chat');
```
```diff
@@ -41,4 +43,16 @@
 ┊41┊43┊      return messages;
 ┊42┊44┊    });
 ┊43┊45┊  }
+┊  ┊46┊
+┊  ┊47┊  onInputKeypress({keyCode}: KeyboardEvent): void {
+┊  ┊48┊    if (keyCode == 13) {
+┊  ┊49┊      this.sendMessage();
+┊  ┊50┊    }
+┊  ┊51┊  }
+┊  ┊52┊
+┊  ┊53┊  sendMessage(): void {
+┊  ┊54┊    MeteorObservable.call('addMessage', this.selectedChat._id, this.message).zone().subscribe(() => {
+┊  ┊55┊      this.message = '';
+┊  ┊56┊    });
+┊  ┊57┊  }
 ┊44┊58┊}🚫↵
```
[}]: #

As you can see, we used `addMessage` Method, which doesn't exist yet.

It the method which will add messages to our messages collection and run on both client's local cache and server. We're going to create a `server/imports/methods/methods.ts` file in our server and implement this method:

[{]: <helper> (diff_step 4.16)
#### Step 4.16: Add 'addMessage()' method to api

##### Added server/imports/methods/methods.ts
```diff
@@ -0,0 +1,18 @@
+┊  ┊ 1┊import {Meteor} from 'meteor/meteor';
+┊  ┊ 2┊import {Chats} from "../../../both/collections/chats.collection";
+┊  ┊ 3┊import {Messages} from "../../../both/collections/messages.collection";
+┊  ┊ 4┊
+┊  ┊ 5┊Meteor.methods({
+┊  ┊ 6┊  addMessage(chatId: string, content: string): void {
+┊  ┊ 7┊    const chatExists = !!Chats.collection.find(chatId).count();
+┊  ┊ 8┊
+┊  ┊ 9┊    if (!chatExists) throw new Meteor.Error('chat-not-exists',
+┊  ┊10┊      'Chat doesn\'t exist');
+┊  ┊11┊
+┊  ┊12┊    Messages.collection.insert({
+┊  ┊13┊      chatId: chatId,
+┊  ┊14┊      content: content,
+┊  ┊15┊      createdAt: new Date()
+┊  ┊16┊    });
+┊  ┊17┊  }
+┊  ┊18┊});🚫↵
```
[}]: #

It's not yet visible by server, let's change it:

[{]: <helper> (diff_step 4.17)
#### Step 4.17: Added import for the methods file

##### Changed server/main.ts
```diff
@@ -1,4 +1,5 @@
 ┊1┊1┊import { Main } from './imports/server-main/main';
+┊ ┊2┊import './imports/methods/methods';
 ┊2┊3┊
 ┊3┊4┊const mainInstance = new Main();
 ┊4┊5┊mainInstance.start();
```
[}]: #

We would also like to validate some data sent to methods we define.
For this we're going to use a utility package provided to us by Meteor and it's called `check`. Let's add it to the server:

    $ meteor add check

And we're going use it in our method we just defined:

[{]: <helper> (diff_step 4.19)
#### Step 4.19: Add validations to 'addMessage()' method in api

##### Changed server/imports/methods/methods.ts
```diff
@@ -1,9 +1,18 @@
 ┊ 1┊ 1┊import {Meteor} from 'meteor/meteor';
 ┊ 2┊ 2┊import {Chats} from "../../../both/collections/chats.collection";
 ┊ 3┊ 3┊import {Messages} from "../../../both/collections/messages.collection";
+┊  ┊ 4┊import {check, Match} from 'meteor/check';
+┊  ┊ 5┊
+┊  ┊ 6┊const nonEmptyString = Match.Where((str) => {
+┊  ┊ 7┊  check(str, String);
+┊  ┊ 8┊  return str.length > 0;
+┊  ┊ 9┊});
 ┊ 4┊10┊
 ┊ 5┊11┊Meteor.methods({
 ┊ 6┊12┊  addMessage(chatId: string, content: string): void {
+┊  ┊13┊    check(chatId, nonEmptyString);
+┊  ┊14┊    check(content, nonEmptyString);
+┊  ┊15┊
 ┊ 7┊16┊    const chatExists = !!Chats.collection.find(chatId).count();
 ┊ 8┊17┊
 ┊ 9┊18┊    if (!chatExists) throw new Meteor.Error('chat-not-exists',
```
[}]: #

The `nonEmptyString` function checks if provided value is a String and if it's not empty.

In addition, we would like the view to auto-scroll down whenever a new message is added:

[{]: <helper> (diff_step 4.20)
#### Step 4.20: Add an auto scroller to messages component

##### Changed client/imports/pages/chat/messages-page.component.ts
```diff
@@ -1,8 +1,8 @@
-┊1┊ ┊import {Component, OnInit} from "@angular/core";
+┊ ┊1┊import {Component, OnInit, OnDestroy} from "@angular/core";
 ┊2┊2┊import {NavParams} from "ionic-angular";
 ┊3┊3┊import {Chat} from "../../../../both/models/chat.model";
 ┊4┊4┊import {Messages} from "../../../../both/collections/messages.collection";
-┊5┊ ┊import {Observable} from "rxjs";
+┊ ┊5┊import {Observable, Subscription} from "rxjs";
 ┊6┊6┊import {Message} from "../../../../both/models/message.model";
 ┊7┊7┊import template from "./messages-page.component.html";
 ┊8┊8┊import style from "./messages-page.component.scss";
```
```diff
@@ -15,12 +15,13 @@
 ┊15┊15┊    style
 ┊16┊16┊  ]
 ┊17┊17┊})
-┊18┊  ┊export class MessagesPage implements OnInit {
+┊  ┊18┊export class MessagesPage implements OnInit, OnDestroy {
 ┊19┊19┊  private selectedChat: Chat;
 ┊20┊20┊  private title: string;
 ┊21┊21┊  private picture: string;
 ┊22┊22┊  private messages: Observable<Message[]>;
 ┊23┊23┊  private message = "";
+┊  ┊24┊  private autoScroller: Subscription;
 ┊24┊25┊
 ┊25┊26┊  constructor(navParams: NavParams) {
 ┊26┊27┊    this.selectedChat = <Chat>navParams.get('chat');
```
```diff
@@ -42,6 +43,38 @@
 ┊42┊43┊
 ┊43┊44┊      return messages;
 ┊44┊45┊    });
+┊  ┊46┊
+┊  ┊47┊    this.autoScroller = MeteorObservable.autorun().subscribe(() => {
+┊  ┊48┊      this.scroller.scrollTop = this.scroller.scrollHeight;
+┊  ┊49┊      this.messageEditor.focus();
+┊  ┊50┊    });
+┊  ┊51┊  }
+┊  ┊52┊
+┊  ┊53┊  private get messagesPageContent(): Element {
+┊  ┊54┊    return document.querySelector('.messages-page-content');
+┊  ┊55┊  }
+┊  ┊56┊
+┊  ┊57┊  private get messagesPageFooter(): Element {
+┊  ┊58┊    return document.querySelector('.messages-page-footer');
+┊  ┊59┊  }
+┊  ┊60┊
+┊  ┊61┊  private get messagesList(): Element {
+┊  ┊62┊    return this.messagesPageContent.querySelector('.messages');
+┊  ┊63┊  }
+┊  ┊64┊
+┊  ┊65┊  private get messageEditor(): HTMLInputElement {
+┊  ┊66┊    return <HTMLInputElement>this.messagesPageFooter.querySelector('.message-editor');
+┊  ┊67┊  }
+┊  ┊68┊
+┊  ┊69┊  private get scroller(): Element {
+┊  ┊70┊    return this.messagesList.querySelector('.scroll-content');
+┊  ┊71┊  }
+┊  ┊72┊
+┊  ┊73┊  ngOnDestroy() {
+┊  ┊74┊    if (this.autoScroller) {
+┊  ┊75┊      this.autoScroller.unsubscribe();
+┊  ┊76┊      this.autoScroller = undefined;
+┊  ┊77┊    }
 ┊45┊78┊  }
 ┊46┊79┊
 ┊47┊80┊  onInputKeypress({keyCode}: KeyboardEvent): void {
```
[}]: #

[}]: #
[{]: <region> (footer)
[{]: <helper> (nav_step)
| [< Previous Step](step3.md) | [Next Step >](step5.md) |
|:--------------------------------|--------------------------------:|
[}]: #
[}]: #