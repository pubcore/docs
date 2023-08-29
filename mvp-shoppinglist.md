# Minimum Viable Product (MVP) Shopping List

In order to learn something about the [mongoose](https://mongoosejs.com/) 
library for mongodb i decided to create a shopping list application.

## MVP
A user opens a page (like a paper) and just write articles to the list. 
He may remove or edit existing articles. The user can sent the list to someone else.
Every user can do everything at any time with the list.

## Development Concept
### Prerequisites
1. Usage of mongoose and mongodb
1. Build as web application based on JavaScript/TypeScript with node.js and React

### Concept
#### Data Model, Resource oriented, definition by example representation in JSON
##### Shopping List
```
{
    "_id": "5e0c0a0b7b1c2b0001c0b1c2",
    "lastModified": "2020-01-01T00:00:00.000Z",
    "articles": [
        {
            "text": "Milk"
        },
    ]
}
```

#### User Interface
1. Landing page with just one button "New shopping list" (SL)
1. Initial SL: One longe input field for new article
1. Keyboard support, for instance save on enter key press
1. SL: Touch on article activates the input field
1. SL: Only one article can be edited at the same time
1. Autosave, if article text is changed

#### MongoDB
1. Leverage fre tier of [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)

#### CI/CD
1. [GitHub Actions](https://github.com/features/actions) in context of free tier of github

#### Hosting
1. Testing of vercel.com hosting

#### Operational Questions
1. How to make a list only available to users it has been sent to?
   * URI to resource with global unique id, which is not (really) guessable
1. How to avoid flooding of the database with shopping lists?   
   * long term: delete shopping lists after a certain time
   * short term: limit the number of shopping lists per IP address. 
     * Use of IP address is a privacy concern.
     * Other options: 
       * Use of captcha or compuation effort to create a new list
       * Global ratelimit: only a certain number of lists per time period