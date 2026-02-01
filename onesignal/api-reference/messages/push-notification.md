> ## Documentation Index
>
> Fetch the complete documentation index at: https://documentation.onesignal.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Push notification

> Send a message using the push notification channel.

## Overview

The Create message API allows you to send push notifications, emails, and SMS to your users. This guide is specific for push. See [Email](/reference/email) or [SMS](/reference/sms) to send to those channels.

Ensure your application is properly configured by following the [Mobile SDK Setup](/docs/en/mobile-sdk-setup) and/or [Web SDK Setup](/docs/en/web-sdk-setup) guides. You should see subscribed push [Subscriptions](/docs/en/subscriptions) in your OneSignal dashboard to send them messages.

<Note>
  Review the [Sending messages with the OneSignal API](/reference/create-message) guide for details on how to structure your messages.

- [Select your target audience](/reference/create-message#choose-your-target-audience)
- [Craft your message](/reference/create-message#craft-your-message)
- [Schedule & per-user delivery options](/reference/create-message#schedule-%26-per-user-delivery)
  </Note>

---

## OpenAPI

```yaml POST /notifications?c=push
openapi: 3.1.0
info:
  title: api.onesignal.com
  version: "11.6"
servers:
  - url: https://api.onesignal.com
security:
  - {}
paths:
  /notifications?c=push:
    post:
      summary: Push notification
      description: Send a message using the push notification channel.
      operationId: push-notification
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
                - contents
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
                  default: push
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
                contents:
                  type: object
                  description: >-
                    The main message body with [language-specific
                    values](/docs/multi-language-messaging#supported-languages).
                    Supports [Message
                    Personalization](/docs/message-personalization).
                  required:
                    - en
                  properties:
                    en:
                      type: string
                      description: >-
                        The required message language type. See [Supported
                        Languages](/docs/multi-language-messaging#supported-languages).
                      default: Default message.
                headings:
                  type: object
                  description: >-
                    The message title with [language-specific
                    values](/docs/multi-language-messaging#supported-languages).
                    Required for Huawei and Web Push. If not set for Web Push,
                    it defaults to your 'Site Name'. Not required if using
                    `template_id` or `content_available`. Supports [Message
                    Personalization](/docs/message-personalization) and must
                    include the same languages as `contents` to ensure
                    localization consistency.
                  properties:
                    en:
                      type: string
                      description: >-
                        The title in English. If used, must include the same
                        languages as `contents`.
                subtitle:
                  type: object
                  description: >-
                    iOS only. The subtitle with [language-specific
                    values](/docs/multi-language-messaging#supported-languages).
                    Supports [Message
                    Personalization](/docs/message-personalization) and must
                    include the same languages as `contents` to ensure
                    localization consistency.
                  properties:
                    en:
                      type: string
                      description: >-
                        The subtitle for iOS push only. If used, must include
                        the same languages as `contents`.
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
                ios_attachments:
                  type: object
                  description: >-
                    The local name or URL of the media attachment to include in
                    your notification. Users can expand the notification to view
                    images, videos, or other supported attachments. See [Images
                    & Rich Media](/docs/rich-media).
                  properties:
                    id:
                      type: string
                      description: >-
                        The URL of the media to display in the notification.
                        Example:
                        `https://avatars.githubusercontent.com/u/11823027?s=200&v=4`
                big_picture:
                  type: string
                  description: >-
                    The local name or URL of the image to include in your Google
                    Android notification. Users can expand the notification to
                    view the images. See [Images & Rich
                    Media](/docs/rich-media).
                huawei_big_picture:
                  type: string
                  description: >-
                    The local name or URL of the image to include in your Huawei
                    Android notification. Users can expand the notification to
                    view the images. See [Images & Rich
                    Media](/docs/rich-media).
                adm_big_picture:
                  type: string
                  description: >-
                    The local name or URL of the image to include in your Amazon
                    Android notification. Users can expand the notification to
                    view the images. See [Images & Rich
                    Media](/docs/rich-media).
                chrome_web_image:
                  type: string
                  description: >-
                    The URL of the image to include in your Chrome notification.
                    Users can expand the notification to view the images.
                    Supported on Chrome for Windows and Android. macOS does not
                    support this parameter and instead expands the
                    `chrome_web_icon`. See [Images & Rich
                    Media](/docs/rich-media).
                small_icon:
                  type: string
                  description: >-
                    The local name of the small icon to display in the Google
                    Android notification. See [Notification
                    icons](/docs/notification-icons).
                huawei_small_icon:
                  type: string
                  description: >-
                    The local name of the small icon to display in the Huawei
                    Android notification. See [Notification
                    icons](/docs/notification-icons).
                adm_small_icon:
                  type: string
                  description: >-
                    The local name of the small icon to display in the Amazon
                    Android notification. See [Notification
                    icons](/docs/notification-icons).
                large_icon:
                  type: string
                  description: >-
                    The local name or URL of the large icon to display in the
                    Google Android notification. See [Notification
                    icons](/docs/notification-icons).
                huawei_large_icon:
                  type: string
                  description: >-
                    The local name or URL of the large icon to display in the
                    Huawei Android notification. See [Notification
                    icons](/docs/notification-icons).
                adm_large_icon:
                  type: string
                  description: >-
                    The local name or URL of the large icon to display in the
                    Amazon Android notification. See [Notification
                    icons](/docs/notification-icons).
                chrome_web_icon:
                  type: string
                  description: >-
                    The URL of the icon to display in the Chrome web
                    notification. Defaults to the resource set in the OneSignal
                    dashboard. See [Notification
                    icons](/docs/notification-icons).
                firefox_icon:
                  type: string
                  description: >-
                    The URL of the icon to display in the Firefox web
                    notification. Defaults to the resource set in the OneSignal
                    dashboard. See [Notification
                    icons](/docs/notification-icons).
                chrome_web_badge:
                  type: string
                  description: >-
                    The URL of the icon to display in the Android notification
                    tray for Chrome web notifications. Defaults to the Chrome
                    icon. See [Push](/docs/push#badges).
                android_channel_id:
                  type: string
                  description: >-
                    The UUID of the [Android notification channel
                    category](/docs/android-notification-categories) created
                    within your OneSignal app.
                existing_android_channel_id:
                  type: string
                  description: >-
                    The UUID of the [Android notification channel
                    category](/docs/android-notification-categories) created
                    within your Android app.
                huawei_channel_id:
                  type: string
                  description: >-
                    The UUID of the [Android notification channel
                    category](/docs/android-notification-categories) created
                    within your OneSignal app.
                huawei_existing_channel_id:
                  type: string
                  description: >-
                    The UUID of the [Android notification channel
                    category](/docs/android-notification-categories) created
                    within your Huawei app.
                huawei_category:
                  type: string
                  description: >-
                    The category you set for notifications sent to Huawei
                    devices. The category chosen must align with an approved
                    [self-classification
                    application](https://developer.huawei.com/consumer/cn/doc/HMSCore-Guides/message-classification-0000001149358835#section1653845862216).
                    Subject to daily send limitations ranging from 2 to 5,
                    depending on the specific [third-level
                    classifications](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/message-restriction-description-0000001361648361#section199311418515)
                    the message falls under.
                  enum:
                    - MARKETING
                    - IM
                    - VOIP
                    - SUBSCRIPTION
                    - TRAVEL
                    - HEALTH
                    - WORK
                    - ACCOUNT
                    - EXPRESS
                    - FINANCE
                    - DEVICE_REMINDER
                    - MAIL
                  default: MARKETING
                huawei_msg_type:
                  type: string
                  description: >-
                    The type of notification being sent to Huawei devices.
                    Options: `message` - (default) For displayable notifications
                    to the user. Notification will be shown even if the app is
                    force quit. If the device is offline it will display the
                    notification when it connects to the internet within the
                    `ttl` timeframe (usually 3 days). Does not support
                    [Confirmed Delivery](/docs/confirmed-delivery#huawei),
                    Huawei requires using their dashboard to track this. `data`
                    - used for notifications containing data payloads you intend
                    to process in the background. If the app is force quit, HMS
                    Core will not start the app to process the notification.
                    Supports [Confirmed
                    Delivery](/docs/confirmed-delivery#huawei).
                  enum:
                    - message
                    - data
                  default: message
                huawei_bi_tag:
                  type: string
                  description: >-
                    Define a tag for associating messages in a batch delivery,
                    facilitating precise monitoring and analysis of delivery
                    stats. This tag is returned to your server when Huawei's
                    Push Kit sends a message receipt. You can set this parameter
                    to track your push campaigns' performance and optimize your
                    messaging strategy.
                priority:
                  type: integer
                  description: >-
                    Set the priority based on the urgency of the message. `10` -
                    High priority. `5` - Normal priority. Recommended and
                    default value is `10`. APNs and FCM use this parameter to
                    determine how quickly a notification is delivered and
                    processed, particularly in power-saving modes. If sending
                    data/background notifications, `5` (Normal priority) is
                    recommended. For details, see [APNs
                    `apns-priority`](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)
                    and [FCM
                    `priority`](https://firebase.google.com/docs/cloud-messaging/android/message-priority).
                  format: int32
                  enum:
                    - 10
                    - 5
                  default: 10
                ios_interruption_level:
                  type: string
                  description: >-
                    The priority and delivery timing of iOS notifications based
                    on their importance and the urgency with which they should
                    interrupt the user. See [iOS Focus modes and interruption
                    levels](/docs/ios-focus-modes-and-interruption-levels).
                  enum:
                    - active
                    - passive
                    - time_sensitive
                    - critical
                  default: active
                ios_sound:
                  type: string
                  description: >-
                    The local name of the custom sound file to play when the
                    notification is received instead of the default sound. See
                    [Notification sounds](/docs/notification-sounds).
                ios_badgeType:
                  type: string
                  description: >-
                    Set or increment the badge count on iOS devices. Use with
                    `ios_badgeCount`. See [Badges](/docs/badges).
                  enum:
                    - None
                    - SetTo
                    - Increase
                  default: None
                ios_badgeCount:
                  type: integer
                  description: >-
                    Use with `ios_badgeType` to determine the numerical change
                    to your app's badge count. See [Badges](/docs/badges).
                  format: int32
                android_accent_color:
                  type: string
                  description: >-
                    The ARGB Hex formatted color of the Android small icon
                    background. For Android 8+ use [Android notification channel
                    category](/docs/android-notification-categories) and
                    `android_channel_id`.
                huawei_accent_color:
                  type: string
                  description: >-
                    The ARGB Hex formatted color of the Huawei small icon
                    background. For Android 8+ use [Android notification channel
                    category](/docs/android-notification-categories) and
                    `huawei_channel_id`.
                url:
                  type: string
                  description: >-
                    The `https`URL that opens in the browser when a user
                    interacts with the notification. See [URLs, Links and Deep
                    Links](/docs/links). Supports [Message
                    Personalization](/docs/message-personalization).
                app_url:
                  type: string
                  description: >-
                    Similar to the `url` parameter but exclusively targets
                    mobile platforms like iOS, Android. Accepts values other
                    than `https` but must use `your-app-scheme://` protocol.
                web_url:
                  type: string
                  description: >-
                    Use with `app_url` if your app and website need different
                    URLs. Accepts URLs with protocol `https://`
                target_content_identifier:
                  type: string
                  description: >-
                    Direct the notification to a specific user experience within
                    your app, such as an App Clip, or target a particular window
                    in applications that use multiple scenes. See [Apple's
                    documentation](https://developer.apple.com/documentation/foundation/nsuseractivity/3238062-targetcontentidentifier).
                buttons:
                  type: array
                  description: >-
                    Add a maximum of 3 Action Buttons to Android and iOS push
                    notifications. See [Action Buttons](/docs/action-buttons).
                  maxItems: 3
                  items:
                    properties:
                      id:
                        type: string
                        description: >-
                          The ID to reference the button clicked event in your
                          app.
                      text:
                        type: string
                        description: The text to display on the button.
                      icon:
                        type: string
                        description: The local name of the icon to display on the button.
                    required:
                      - id
                      - text
                    additionalProperties: false
                web_buttons:
                  type: array
                  description: >-
                    Add a maximum of 2 Action Buttons to Chrome web push
                    notifications. See [Action Buttons](/docs/action-buttons).
                  maxItems: 2
                  items:
                    properties:
                      id:
                        type: string
                        description: >-
                          The ID to reference the button clicked event in your
                          app.
                      text:
                        type: string
                        description: The text to display on the button.
                      url:
                        type: string
                        description: The URL to open when the button is clicked.
                    required:
                      - id
                      - text
                      - url
                    additionalProperties: false
                thread_id:
                  type: string
                  description: >-
                    An ID to group notifications on Apple devices. Notifications
                    with the same identifier are organized together in the
                    notification center.
                ios_relevance_score:
                  type: number
                  description: >-
                    A value between `0` and `1`, to sort the notifications from
                    your app. The highest score gets featured in the
                    notification summary. See [iOS Relevance
                    Score](/docs/ios-relevance-score)
                  format: double
                android_group:
                  type: string
                  description: >-
                    An ID to group notifications on Google Android devices.
                    Notifications with the same identifier are organized
                    together in the notification center.
                adm_group:
                  type: string
                  description: >-
                    An ID to group notifications on Amazon Android devices.
                    Notifications with the same identifier are organized
                    together in the notification center.
                ttl:
                  type: integer
                  description: >-
                    The duration in seconds for which a notification remains
                    valid if the device is offline. Any number between `0` and
                    `2419200` (28 days). Defaults to 3 days. See [Push: Time to
                    Live](/docs/push#time-to-live).
                  format: int32
                  default: 259200
                collapse_id:
                  type: string
                  description: >-
                    An ID that replaces older notifications with newer ones that
                    have the same identifier. For mobile push only. See [Push:
                    Collapse ID](/docs/push#collapse-id).
                web_push_topic:
                  type: string
                  description: >-
                    An ID that prevents replacement of older notifications with
                    newer ones that have different identifiers. For web push
                    only. See [Push: Web Push Topic](/docs/push#web-push-topic).
                data:
                  type: object
                  description: >-
                    Bundle a custom data map within your notification, which is
                    then passed to your app. See [Push: Additional
                    Data](/docs/push#additional-data).
                  format: json
                content_available:
                  type: boolean
                  description: >-
                    Allows for sending data/background notifications to the
                    Android and iOS apps. Set to `true` and omit `contents`.
                    Apple interprets this as `content-available=1`. See [Data &
                    background notifications](/docs/data-notifications).
                ios_category:
                  type: string
                  description: >-
                    Enable users to respond directly to a notification without
                    launching the app. The
                    [Category](https://developer.apple.com/documentation/usernotifications/unnotificationcategory)
                    will activate the corresponding [Notification Content
                    Extension](https://developer.apple.com/documentation/usernotificationsui/unnotificationcontentextension/)
                    in your app when the push is interacted with.
                apns_push_type_override:
                  type: string
                  description: >-
                    Use only for VoIP notifications. Corresponds to the
                    [`apns-push-type`](https://developer.apple.com/documentation/usernotifications/sending-notification-requests-to-apns#Send-a-POST-request-to-APNs).
                    OneSignal automatically sets this value to `alert` or
                    `background` based on the notification content. Pass `voip`
                    to initiate VoIP calls or alert the user to incoming VoIP
                    calls.
                isIos:
                  type: boolean
                  description: >-
                    Specifies if the notification should target iOS mobile apps
                    only. Defaults to `true`. If set to `true`, all other
                    platforms are disabled unless explicitly enabled.
                isAndroid:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Google Android
                    mobile apps only. Defaults to `true`. If set to `true`, all
                    other platforms are disabled unless explicitly enabled.
                isHuawei:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Huawei mobile
                    apps only. Defaults to `true`. If set to `true`, all other
                    platforms are disabled unless explicitly enabled.
                isAnyWeb:
                  type: boolean
                  description: >-
                    Specifies if the notification should target web push only.
                    Defaults to `true`. If set to `true`, all other platforms
                    are disabled unless explicitly enabled.
                isChromeWeb:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Chrome only.
                    Defaults to `true`. If set to `true`, all other platforms
                    are disabled unless explicitly enabled.
                isFirefox:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Firefox only.
                    Defaults to `true`. If set to `true`, all other platforms
                    are disabled unless explicitly enabled.
                isSafari:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Safari only.
                    Defaults to `true`. If set to `true`, all other platforms
                    are disabled unless explicitly enabled
                isWP_WNS:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Windows apps
                    only. Defaults to `true`. If set to `true`, all other
                    platforms are disabled unless explicitly enabled
                isAdm:
                  type: boolean
                  description: >-
                    Specifies if the notification should target Amazon devices
                    only. Defaults to `true`. If set to `true`, all other
                    platforms are disabled unless explicitly enabled
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
                throttle_rate_per_minute:
                  type: number
                  description: >-
                    Overrides the throttle limit set in the OneSignal dashboard
                    settings. Must be enabled through the dashboard. Only
                    available with push notifications. See [Push
                    Throttling](/docs/throttling). If `throttle_rate_per_minute`
                    is set to `0`, then the message will be sent immediately
                    without any rate limiting.
                enable_frequency_cap:
                  type: boolean
                  description: >-
                    Overrides the frequency cap set in the OneSignal dashboard
                    settings. Must be enabled through the dashboard first. Only
                    available with push notifications. See [Frequency
                    Capping](/docs/frequency-capping). Set to `false` to disable
                    frequency capping.
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
                                    unsubscribed Subscriptions. The
                                    Subscriptions associated with the listed
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
                                    unsubscribed Subscription. The Subscriptions
                                    associated with the listed OneSignal IDs
                                    were unsubscribed before the message was
                                    sent. In this example, the user with
                                    OneSignal ID
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
