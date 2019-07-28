## OpenHentai

### Overview

**Goal**: To have a permanent database of hentai doujin & manga etc.

**Summary**: use peer to peer sharing to replicate complete copy of the hentai database to every peer, whom can read this database. The database will only contain metadata - eg {title, tags, link (torrent, magnet)}, this will reduce the size of the database. Only people from the list of contributers can write to this database.

**Users**: From a user perspective, they visit a website which gives them a javascript client. The client will do peer discovery, and then download the latest version of the database from a peer. While the website is open, the client will remain a peer for other users. Using local storage means revisiting the website will only need to fetch updates since the web page was closed.

**User Interface**: The user interface will allow users to search for hentai from the database. It will include javascript to download the torrent file. The client will allow extensibility - eg auto-download specific tags. The client will let users label and star items, however these will be local - they will not be shared. This means you can't see average ratings by all users of an item.

### Technical Details

**Protocol**

`GetAll`: get all patches the peer has

`GetSince(date)`: get all patches newer than `date`

`PushPatch(patch)`: receive patch, this helps increase the rate of seeding changes

**Replicated Data Structure**

We replicate immutable patches that represent changes to rows in a relational database. These patches are ordered via the date field and merged together to form the actual database row.

| field     | description                |
|-----------|----------------------------|
| user      | a public signing key       |
| table     | table name                 |
| id        | table row primary key      |
| date      | date the row was edited    |
| data      | table data                 |
| signature | sign data with private key |

**Security**

In order to prevent spam and unwanted edits, we have a list of users. The root user is a special user hardcoded into OpenHentai client, who has the power to edit the `Users table`:

| field         | description               |
|---------------|---------------------------|
| user          | a public signing key      |
| tables        | tables this user can edit |

When a user is in this table, they can share patches and get them replicated. When a patch is received from any user not in this table, the patch is ignored.
