
# Stacks-Velmora - Decentralized Content Curation Contract

This Clarity smart contract enables decentralized submission, appraisal, flagging, and rewarding of user-generated content on the Stacks blockchain. It promotes community moderation, reputation scoring, and micro-incentives to maintain a high-quality content ecosystem.

---

## 🧠 Overview

### 🔍 Key Features

* **Content Submission**: Users can submit articles or media with metadata.
* **Voting/Appraisals**: Community members can upvote or downvote content.
* **Reputation System**: Voters earn credibility based on their appraisal behavior.
* **Gratuities**: Users can reward content creators using STX tips.
* **Moderation**: Community can flag inappropriate content.
* **Admin Controls**: Protocol administrator can manage fees, topics, and content.

---

## 📦 Contract Components

### Constants

| Name                     | Type        | Description                                     |
| ------------------------ | ----------- | ----------------------------------------------- |
| `PROTOCOL_ADMINISTRATOR` | `principal` | Contract deployer, admin role.                  |
| `submission-charge`      | `uint`      | Fee required to submit content. Default: `u10`. |
| `MIN_HYPERLINK_LENGTH`   | `u10`       | Minimum length of the hyperlink.                |
| `MAX_UINT`               | `uint`      | Maximum allowed value for safety.               |

### Errors

| Error Code | Description                     |
| ---------- | ------------------------------- |
| `u100`     | Unauthorized access.            |
| `u101`     | Submission validation failed.   |
| `u102`     | Duplicate content.              |
| `u103`     | Nonexistent item.               |
| `u104`     | Not enough balance.             |
| `u105`     | Invalid topic.                  |
| `u106`     | Invalid flag (e.g., self-flag). |
| `u107`     | Overflow error.                 |
| `u108`     | Invalid appraisal value.        |
| `u109`     | Invalid item identifier.        |

---

## 🗂 Data Structures

### Variables

* `submission-charge`: Fee to contribute content.
* `aggregate-submissions`: Counter for item IDs.
* `content-topics`: List of whitelisted topic strings.

### Maps

* `curated-items`: Maps item ID → content metadata.
* `participant-appraisals`: Tracks user votes.
* `participant-credibility`: Tracks user reputation score.

---

## ⚙️ Contract Functions

### 📤 Public Functions

#### `contribute-item (headline, hyperlink, topic)`

Submit new content with metadata.

* Requirements: Sufficient STX, valid topic, non-empty fields.
* Returns: Item ID.

#### `appraise-item (item-identifier, appraisal)`

Vote on content.

* Appraisal: Must be `1` (upvote) or `-1` (downvote).
* Updates:

  * `curated-items.appraisals`
  * `participant-appraisals`
  * `participant-credibility`

#### `reward-originator (item-identifier, gratuity-amount)`

Send STX tips to content creators.

* STX is transferred to content `originator`.
* Gratuity is recorded in metadata.

#### `flag-item (item-identifier)`

Flag inappropriate content.

* Cannot flag your own post.
* Increments `flags`.

---

### 🔍 Read-Only Functions

#### `retrieve-item-details (item-identifier)`

Fetch all metadata of an item.

#### `retrieve-participant-appraisal (participant, item-identifier)`

Get appraisal from a user on an item.

#### `retrieve-aggregate-submissions`

Returns the total number of submissions.

#### `retrieve-participant-credibility (participant)`

Returns reputation score of a participant.

#### `retrieve-top-items (limit)`

Returns items with non-negative appraisal values.

* Limited to 10 items max (for now).

---

### 🔐 Admin Functions

#### `adjust-submission-charge (new-charge)`

Sets a new STX fee for content submission.

* Only the admin can call this.

#### `expunge-item (item-identifier)`

Deletes an item from storage.

* Only the admin can call this.

#### `introduce-topic (new-topic)`

Adds a new content topic to the whitelist.

* Max 10 topics allowed.
* Only the admin can call this.

---

## 📈 Reputation System

Participants earn or lose credibility:

* ✅ Upvote (+1)
* ❌ Downvote (-1)
* Credibility is cumulative and stored in `participant-credibility`.

---

## 📑 Example Workflows

### 🔹 Submit Content

```lisp
(contribute-item "Decentralized Voting" "https://example.com/article" "Politics")
```

### 🔹 Vote on Content

```lisp
(appraise-item u1 1) ;; Upvote
(appraise-item u1 -1) ;; Downvote
```

### 🔹 Tip Creator

```lisp
(reward-originator u1 u50)
```

---
