[![CircleCI](https://circleci.com/gh/opendialogai/webchat/tree/master.svg?style=svg&circle-token=ef6ed717ecefce0b6acabcd01a40dc913370412a)](https://circleci.com/gh/opendialogai/webchat/tree/master)

# Steps to use this package

## Composer set up

Require as a dependency in your composer.json file

+ Add the repository to the repositories section:

             {
                 "type": "git",
                 "url": "https://github.com/GreenShootLabs/OpenDialog-Webchat.git"
             }
                   
+ Install the composer dependencies with `composer update`

## Front end set up

+ Change to the `vendor/opendialog/webchat-frontend` directory and run `npm install; npm run dev` (for development)

+ Publish the update script by running:
    + `php artisan vendor:publish --tag=scripts`
    
+ This should move a script named `update-web-chat.sh` to your project root. Run it every time there is an update to OpenDialog webchat by running:
```bash update-web-chat.sh``` 

## Git Hooks

To set up the included git pre-commit hook, first make sure the pre-commit script is executable by running

```chmod +x .githooks/pre-commit```

Then configure your local git to use this directory for git hooks by running:

```git config core.hooksPath .githooks/```

Now every commit you make will trigger php codesniffer to run. If there is a problem with the formatting
of the code, the script will echo the output of php codesniffer. If there are no issues, the commit will
go into git.

# Configuration 

The webchat configuration can be found in the `webchat_settings` table. Before running, the config table should be seeded
by running:

```php artisan webchat:settings```

This will load a row for each available setting.

Config items can still be overwritten in the `openDialogSettings` javascript variable that is added to the parent web page.

# Features

## Chat Open param

If the url of the page hosting the chatbot has the query param 'chat_open=true', the chat will load pre-opened

## Callback ID of open event

When the chat window is opened, and default callback_id of welcome is sent along with the chat open event. This can be
overridden by setting the 'callback_id' query param on the url of the page hosting the chatbot. Whatever value is set
as the query param will be sent with the chat open event. It is then down to the user to set up a conversation that
deals with that callback_id 

## Comments tab

The webchat widget can fetch comments from an endpoint matching the JSON:API spec. To enable comments, add a
commentsEnabled parameter to openDialogSettings, set to true. You will additionally need to pass information
about the comment entity name, author entity name, and section entity name. (If present)

Example config:

```javascript
window.openDialogSettings = {
    url: "{{env('APP_URL')}}",
    user : {
        first_name: 'Jane',
        last_name: 'Smith',
        email: 'jane.smith@opendialog.ai',
        external_id: "{{ auth()->user()->id }}",
    },
    comments: {
        commentsEnabled: true,
        commentsName: 'Comments',
        commentsAxiosConfig: {
            baseURL: 'http://localhost/json/',
            headers: {
                // eslint-disable-next-line no-undef
                Authorization: `Bearer {{ auth()->user()->api_token }}`,
                'Content-Type': 'application/vnd.api+json',
            },
        },
        commentsEntityName: 'comments',
        commentsCreatedFieldName: 'created',
        commentsTextFieldName: 'comment',
        commentsAuthorEntityName: 'people',
        commentsAuthorRelationshipName: 'commentAuthor',
        commentsAuthorIdFieldName: 'id',
        commentsAuthorNameFieldName: 'name',
        commentsSectionEntityName: 'posts',
        commentsSectionRelationshipName: 'post',
        commentsSectionIdFieldName: 'id',
        commentsSectionNameFieldName: 'title',
        commentsSectionPathPattern: 'home\\\/posts\\\/(\\\d*)$',
    },
};
```

## Running Code Sniffer
To run code sniffer, run the following command
```./vendor/bin/phpcs --standard=od-cs-ruleset.xml src/ --ignore=*/migrations/*,*/tests/*```
