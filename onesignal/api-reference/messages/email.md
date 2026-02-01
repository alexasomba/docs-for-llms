> ## Documentation Index
>
> Fetch the complete documentation index at: https://documentation.onesignal.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Email

> Send a message using the email channel.

## Overview

The Create message API allows you to send push notifications, emails, and SMS to your users. This guide is specific for email. See [Push notification](/reference/push-notification) or [SMS](/reference/sms) to send to those channels.

Ensure your [Email setup](/docs/en/email-setup) is complete.

<Note>
  Review the [Sending messages with the OneSignal API](/reference/create-message) guide for details on how to structure your messages.

- [Select your target audience](/reference/create-message#choose-your-target-audience)
- [Craft your message](/reference/create-message#craft-your-message)
- [Schedule & per-user delivery options](/reference/create-message#schedule-%26-per-user-delivery)
  </Note>

---

## OpenAPI

```yaml POST /notifications?c=email
openapi: 3.1.0
info:
  title: api.onesignal.com
  version: "11.6"
servers:
  - url: https://api.onesignal.com
security:
  - {}
paths:
  /notifications?c=email:
    post:
      summary: Email
      description: Send a message using the email channel.
      operationId: email
      parameters:
        - name: Authorization
          in: header
          description: >-
            Your App API key with prefix `Key `. See [Keys &
            IDs](/docs/keys-and-ids).
          required: true
          schema:
            type: string
            default: Key YOUR_APP_API_KEY
      requestBody:
        content:
          application/json:
            schema:
              type: object
              required:
                - app_id
                - email_subject
                - email_body
              properties:
                app_id:
                  type: string
                  description: >-
                    Your OneSignal App ID in UUID v4 format. See [Keys &
                    IDs](/docs/keys-and-ids).
                  default: YOUR_APP_ID
                include_aliases:
                  type: object
                  description: >-
                    Target up to 20,000 users by their `external_id`,
                    `onesignal_id`, or your own custom alias. Use with
                    `target_channel` to control the delivery channel. Not
                    compatible with any other targeting parameters like
                    `filters`, `include_subscription_ids`, `included_segments`,
                    or `excluded_segments`. See [Sending messages with the
                    OneSignal API](/reference/create-message#include-aliases).
                  format: json
                  properties:
                    external_id:
                      description: >-
                        An array of external IDs which should be the same as the
                        user ID in your app. This is the recommended method for
                        targeting users. See [Users](/docs/users).
                      type: array
                      items:
                        type: string
                target_channel:
                  type: string
                  description: >-
                    The targeted delivery channel. Required when using
                    `include_aliases`. Accepts `push`, `email`, or `sms`.
                  enum:
                    - push
                    - email
                    - sms
                  default: email
                include_subscription_ids:
                  type: array
                  description: >-
                    Target users' specific [subscriptions](/docs/subscriptions)
                    by ID. Include up to 20,000 `subscription_id` per API call.
                    Not compatible with any other targeting parameters like
                    `filters`, `include_aliases`, `included_segments`, or
                    `excluded_segments`. See [Sending messages with the
                    OneSignal API](/reference/create-message).
                  items:
                    type: string
                email_to:
                  type: array
                  description: >-
                    Send email to specific users by their email address. Include
                    up to 20,000 email addresses per API call. If the email
                    address does not exist within the OneSignal App, then a new
                    email Subscription will be created. Can only be used when
                    sending [Email](/reference/email). Not compatible with any
                    other targeting parameters like `filters`,
                    `include_aliases`, `included_segments`, or
                    `excluded_segments`. See [Sending messages with the
                    OneSignal API](/reference/create-message).
                  items:
                    type: string
                included_segments:
                  type: array
                  description: >-
                    Target predefined [Segments](/docs/segmentation). Users that
                    are in multiple segments will only be sent the message once.
                    Can be combined with `excluded_segments`. Not compatible
                    with any other targeting parameters like `filters`,
                    `include_aliases`, or `include_subscription_ids`. See
                    [Sending messages with the OneSignal
                    API](/reference/create-message).
                  items:
                    type: string
                excluded_segments:
                  type: array
                  description: >-
                    Exclude users in predefined [Segments](/docs/segmentation).
                    Overrides membership in any segment specified in the
                    `included_segments`. Not compatible with any other targeting
                    parameters like `filters`, `include_aliases`, or
                    `include_subscription_ids`. See [Sending messages with the
                    OneSignal API](/reference/create-message).
                  items:
                    type: string
                filters:
                  type: array
                  description: >-
                    Filters define the segment based on user properties like
                    tags, activity, or location using flexible AND/OR logic.
                    Limited to 200 total entries, including fields and `OR`
                    operators. See [Sending messages with the OneSignal
                    API](/reference/create-message#filters).
                  items:
                    oneOf:
                      - title: Filter
                        description: Required. The fitler object.
                        required:
                          - field
                          - relation
                        type: object
                        properties:
                          field:
                            type: string
                            description: The name of the filter to use.
                            enum:
                              - tag
                              - last_session
                              - first_session
                              - session_count
                              - session_time
                              - language
                              - app_version
                              - location
                              - country
                          relation:
                            type: string
                            description: >-
                              Used with most filters. See details on the
                              specific filter.
                            enum:
                              - "="
                              - "!="
                              - ">"
                              - <
                              - exists
                              - not_exists
                              - time_elapsed_gt
                              - time_elapsed_lt
                          key:
                            type: string
                            description: Used with the `tag` filter. This is the tag `key`.
                          value:
                            type: string
                            description: >-
                              The value of the `field` or tag `key` in which you
                              want to filter with.
                      - title: Operator
                        type: object
                        properties:
                          operator:
                            type: string
                            description: >-
                              Chain filter conditions with implicit `AND` and
                              `OR` logic. Never end your `filters` object with
                              an `operator`. See
                              [filters](/reference/create-message#filters) for
                              more.
                            enum:
                              - AND
                              - OR
                            default: AND
                  minItems: 1
                  maxItems: 200
                email_subject:
                  type: string
                  description: >-
                    The subject of the email. Supports [Message
                    Personalization](/docs/message-personalization).
                  default: This is your email subject.
                email_preheader:
                  type: string
                  description: Preview text displayed after the email subject.
                email_body:
                  type: string
                  description: >-
                    The body of the email in HTML format. Required if
                    `template_id` is not set. Supports [Message
                    Personalization](/docs/message-personalization).
                name:
                  type: string
                  description: >-
                    An internal name you set to help organize and track
                    messages. Not shown to recipients. Maximum 128 characters.
                template_id:
                  type: string
                  description: >-
                    The template ID in UUID v4 format set for the message if
                    applicable. See [Templates](/docs/templates).
                custom_data:
                  type: object
                  description: >-
                    Include user or context-specific data (e.g., cart items,
                    OTPs, links) in a message. Use with `template_id`. See
                    [Message Personalization](/docs/message-personalization).
                    Max size: 2KB (Push/SMS), 10KB (Email).
                email_from_name:
                  type: string
                  description: >-
                    The name the email is sent from. Defaults to the 'Sender
                    Name' in the Email Settings of your OneSignal Dashboard. See
                    [Email setup](/docs/email-setup) and
                    [Senders](/docs/senders).
                  default: Your Company
                email_from_address:
                  type: string
                  description: >-
                    The full email address shown in the 'From' field of the
                    email (e.g., `promotions@news.example.com`). This is what
                    recipients see as the sender. If not specified, OneSignal
                    uses the default 'Sender Email' set in your Dashboard's
                    Email Settings. See [Senders](/docs/senders).
                email_sender_domain:
                  type: string
                  description: >-
                    The authenticated sending domain used for email delivery.
                    This domain must be verified in your DNS records and will
                    determine which domain handles the mail transfer. It may not
                    always exactly match the domain in the `email_from_address`
                    (e.g., `email_from_address = news@example.com` while
                    `email_sender_domain = mail.example.com`), but the root
                    domain must align for DMARC compliance. If not specified,
                    OneSignal uses the default sender email's domain configured
                    in your Dashboard. See [Email setup](/docs/email-setup) and
                    [Senders](/docs/senders).
                email_reply_to_address:
                  type: string
                  description: >-
                    The email address users reply to. Defaults to the 'Reply-To'
                    address in the Email Settings of your OneSignal Dashboard.
                    See [Email setup](/docs/email-setup).
                include_unsubscribed:
                  type: boolean
                  description: >-
                    Used for important account-related, non-marking emails. If
                    set to `true` it will send the email to unsubscribed email
                    addresses. Defaults to `false`. See [Email unsubscribe links
                    & headers](/docs/unsubscribe-links-email-subscriptions).
                disable_email_click_tracking:
                  type: boolean
                  description: >-
                    If set to `true`, the URLs sent within the email will not
                    include link tracking and will be the same as originally
                    set; otherwise, all the URLs in the email will be tracked.
                    See [Email unsubscribe links &
                    headers](/docs/unsubscribe-links-email-subscriptions).
                    Defaults to `false`.
                send_after:
                  type: string
                  description: >-
                    Schedule delivery for a future date/time (in UTC). The
                    format must be valid per the [ISO
                    8601](https://en.wikipedia.org/wiki/ISO_8601) standard and
                    compatible with [`JavaScript’s Date()
                    parser`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/Date#datestring).
                    Example: `2025-09-24T14:00:00-07:00`
                delayed_option:
                  type: string
                  description: >-
                    Controls how messages are delivered on a per-user basis:
                    `'timezone'` — Sends at the same local time across time
                    zones. `'last-active'` — Delivers based on each user’s most
                    recent session. Not compatible with [Push
                    Throttling](/docs/throttling). If enabled, set
                    `throttle_rate_per_minute` to `0`.
                delivery_time_of_day:
                  type: string
                  description: >-
                    Use with `delayed_option: 'timezone'` to set a consistent
                    local delivery time. Accepted formats: `'9:00AM'` (12-hour),
                    `'21:45'` (24-hour), `'09:45:30'` (HH:mm:ss).
                idempotency_key:
                  type: string
                  description: >-
                    A unique identifier used to prevent duplicate messages from
                    repeat API calls. See [Idempotent notification
                    requests](/reference/idempotent-notification-requests). Any
                    RFC 9562 UUID supported. Valid for 30 days. Previously
                    called `external_id`.
      responses:
        "200":
          description: "200"
          content:
            application/json:
              schema:
                oneOf:
                  - title: Message Sent
                    type: object
                    properties:
                      id:
                        type: string
                        description: >-
                          The message ID in UUID v4 format. If `id` is an empty
                          string, then the message was not sent.
                      external_id:
                        type: string
                        description: >-
                          The `idempotency_key` parameter if set. Used to
                          prevent duplicate messages from repeat API calls. See
                          [Idempotent message
                          requests](/reference/idempotent-notification-requests).
                      errors:
                        type: object
                        description: >-
                          If the message `id` is set, then a message was sent.
                          Any errors reported are with individual Users or
                          Subscriptions that cannot be sent the message.
                        properties:
                          invalid_email_tokens:
                            type: array
                            items:
                              type: string
                              description: >-
                                The listed email addresses used in the
                                `email_to` parameter that were unsubscribed from
                                the email message channel before the message was
                                sent.
                          invalid_aliases:
                            type: object
                            description: >-
                              The alias label that was used in the
                              `include_aliases` parameter.
                            properties:
                              external_id:
                                type: array
                                items:
                                  type: string
                                  description: >-
                                    The alias IDs associated with the
                                    unsubscribed Subscriptions. The listed
                                    aliases were unsubscribed before the message
                                    was sent. In this example, `user_id_1` has
                                    two unsubscribed Subscriptions while
                                    `user_id_2` has one unsubscribed
                                    Subscription.
                                  example: '["user_id_1", "user_id_1", "user_id_2"]'
                              onesignal_id:
                                type: array
                                items:
                                  type: string
                                  description: >-
                                    The OneSignal ID associated with the
                                    unsubscribed Subscription. The listed
                                    OneSignal IDs were unsubscribed before the
                                    message was sent. In this example, the user
                                    with OneSignal ID
                                    `1589641e-bed1-4325-bce4-d2234e578884` has
                                    three unsubscribed Subscriptions.
                                  example: >-
                                    ["1589641e-bed1-4325-bce4-d2234e578884",
                                    "1589641e-bed1-4325-bce4-d2234e578884",
                                    "1589641e-bed1-4325-bce4-d2234e578884"]
                          invalid_player_ids:
                            type: array
                            items:
                              type: string
                              description: >-
                                The Subscription ID exists in the OneSignal app
                                but is unsubscribed from the message channel. If
                                the Subscription ID did not exist, it will not
                                be reported.
                  - title: Message Not Sent
                    type: object
                    properties:
                      id:
                        type: string
                        description: >-
                          If the message `id` is an empty string, then no
                          message was sent. The request appears to be formatted
                          correctly, but there are issues with the aliases,
                          segments, or filters targeted.
                        example: ""
                      errors:
                        type: array
                        description: The reason the message was not sent.
                        items:
                          properties:
                            All included players are not subscribed:
                              type: string
                              description: >-
                                All Subscriptions in the segment or filters are
                                unsubscribed. Check that you spelled the segment
                                names correctly and the segments contain
                                Subscriptions for the message channel being sent
                                to.
        "400":
          description: "400"
          content:
            application/json:
              schema:
                type: object
                properties:
                  errors:
                    type: array
                    description: The reason for the bad request.
                    items:
                      properties:
                        Message Notifications must have English language content:
                          type: string
                          description: >-
                            Make sure the request or template has English
                            language ('en')content. This is required but can be
                            any language desired.
                        "Incorrect subscription_id format in include_subscription_ids (not a valid UUID):":
                          type: string
                          description: The provided `subscription_id` is not a valid UUID.
                        Platforms You may only send to one delivery channel at a time. Make sure you are only including one of push platforms, Email, or SMS.:
                          type: string
                          description: >-
                            You are attempting to send a message to a
                            Subscription for a different channel. Make sure you
                            are only targeting one channel at a time.
      deprecated: false
```
