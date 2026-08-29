# L3ak APT

<figure><img src="../.gitbook/assets/image (1025).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1026).png" alt=""><figcaption></figcaption></figure>



Sau khi tải về và giải nén thì tôi thấy nó giống một Windows collection

<figure><img src="../.gitbook/assets/image (1027).png" alt=""><figcaption></figcaption></figure>

Vì description nói hacker khoe dữ liệu, ta sẽ ưu tiên kiểm tra browser, chat app, torrent/download artifact.

Và Chrome history sẽ lưu dưới dạng db và nằm ở&#x20;

`C/Users/Max/AppData/Local/Google/Chrome/User Data/Default/History`

<figure><img src="../.gitbook/assets/image (1028).png" alt=""><figcaption></figcaption></figure>

Ta thấy được là `7z`  đã được tỉa xuống , ta có thể nghĩ đến 1 số khả năng author đã dùng nó để nén 1 file nào đó

ở trong phần kết quả query ta cũng thấy được sự xuất hiện của các fie torrent

<figure><img src="../.gitbook/assets/image (1029).png" alt=""><figcaption></figcaption></figure>

ta thấy file `./AppData/Roaming/utorrent/important files.torrent`  có vẻ khá khả nghi vì nó có chữ important

Mở file này bằng qBittorrent. Metadata cho thấy torrent chứa:

<figure><img src="../.gitbook/assets/image (1030).png" alt=""><figcaption></figcaption></figure>

but it need password

<figure><img src="../.gitbook/assets/image (1031).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1032).png" alt=""><figcaption></figcaption></figure>

tiếp theo ta sẽ thử check những đoạn cache của app nhắn tin như discord theo như hint từ description

`/home/kali/Desktop/leak/for/C/Users/Max/AppData/Roaming/discord/cache/Cache_Data/`

vì discord dùng chromium nên tôi sẽ dùng CCL Chromium Reader để dump cache

<figure><img src="../.gitbook/assets/image (1033).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

tôi đã filter để lọc ra những cái file chứa tin nhắn

<table><thead><tr><th width="496">file_hash</th><th width="779">key</th></tr></thead><tbody><tr><td>d064ec419dbab66d6db844619455d7ae956feba5942eb7e8fc28005b5dc0d989</td><td>1/0/https://discordapp.com/api/v9/channels/1506324664807587874/messages?limit=10</td></tr><tr><td>fdbfd71f09aa7d65538dc849a3978f228a03b499b12bd8b1ad408295c2e44b5e</td><td>1/0/https://discordapp.com/api/v9/channels/1506332128042811447/messages?limit=10</td></tr><tr><td>f3d9716e4eef625b3ff036e3272d615c7ff4a6feb32327c139c02666f06201f2</td><td>1/0/https://discordapp.com/api/v9/channels/1506332128042811447/messages?before=1511389764140536019&#x26;limit=20</td></tr><tr><td>f4de7f8d4f20450d61c027fa823d1ae9779a6dae009087f2d7570e295f892ccd</td><td>1/0/https://discordapp.com/api/v9/channels/1506332128042811447/messages?before=1511388422785470548&#x26;limit=20</td></tr></tbody></table>

ở file thứ 2&#x20;

```json
[
  {
    "type": 0,
    "content": "innovation is never appreciated in its time",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:25:42.806000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511390362965381251",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "WTF even is that",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:25:37.219000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511390339531804683",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "744254754053685378",
      "username": "ammar4027",
      "avatar": "aa05ac015d49553d62fa0ffb48cd40be",
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "ammar",
      "avatar_decoration_data": {
        "asset": "a_c48b135704ecb5c88f2f71f6c8bcce2f",
        "sku_id": "1357589632581374042",
        "expires_at": null
      },
      "collectibles": {
        "nameplate": {
          "sku_id": "1417311919664005231",
          "asset": "nameplates/nameplate_bonanza/cosmic_storm/",
          "label": "COLLECTIBLES_NAMEPLATE_BONANZA_COSMIC_STORM_NP_A11Y",
          "palette": "violet"
        }
      },
      "display_name_styles": null,
      "banner_color": null,
      "clan": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      },
      "primary_guild": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      }
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "cool aint it",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:25:31.054000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511390313674051654",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "“gime”???",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:24:30.091000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511390057976696933",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "744254754053685378",
      "username": "ammar4027",
      "avatar": "aa05ac015d49553d62fa0ffb48cd40be",
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "ammar",
      "avatar_decoration_data": {
        "asset": "a_c48b135704ecb5c88f2f71f6c8bcce2f",
        "sku_id": "1357589632581374042",
        "expires_at": null
      },
      "collectibles": {
        "nameplate": {
          "sku_id": "1417311919664005231",
          "asset": "nameplates/nameplate_bonanza/cosmic_storm/",
          "label": "COLLECTIBLES_NAMEPLATE_BONANZA_COSMIC_STORM_NP_A11Y",
          "palette": "violet"
        }
      },
      "display_name_styles": null,
      "banner_color": null,
      "clan": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      },
      "primary_guild": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      }
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "people send the word “gime” to @helper123_app_bot and it returns the password",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:24:24.569000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511390034815877291",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "how does it even work",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:24:08.027000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511389965433442556",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "744254754053685378",
      "username": "ammar4027",
      "avatar": "aa05ac015d49553d62fa0ffb48cd40be",
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "ammar",
      "avatar_decoration_data": {
        "asset": "a_c48b135704ecb5c88f2f71f6c8bcce2f",
        "sku_id": "1357589632581374042",
        "expires_at": null
      },
      "collectibles": {
        "nameplate": {
          "sku_id": "1417311919664005231",
          "asset": "nameplates/nameplate_bonanza/cosmic_storm/",
          "label": "COLLECTIBLES_NAMEPLATE_BONANZA_COSMIC_STORM_NP_A11Y",
          "palette": "violet"
        }
      },
      "display_name_styles": null,
      "banner_color": null,
      "clan": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      },
      "primary_guild": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      }
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "extra opsec",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:23:58.276000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511389924534784161",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "why would you do that",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:23:46.157000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511389873704276049",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "744254754053685378",
      "username": "ammar4027",
      "avatar": "aa05ac015d49553d62fa0ffb48cd40be",
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "ammar",
      "avatar_decoration_data": {
        "asset": "a_c48b135704ecb5c88f2f71f6c8bcce2f",
        "sku_id": "1357589632581374042",
        "expires_at": null
      },
      "collectibles": {
        "nameplate": {
          "sku_id": "1417311919664005231",
          "asset": "nameplates/nameplate_bonanza/cosmic_storm/",
          "label": "COLLECTIBLES_NAMEPLATE_BONANZA_COSMIC_STORM_NP_A11Y",
          "palette": "violet"
        }
      },
      "display_name_styles": null,
      "banner_color": null,
      "clan": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      },
      "primary_guild": {
        "identity_guild_id": "473760315293696010",
        "identity_enabled": true,
        "tag": "HTB",
        "badge": "5fbf677de9ff9e82c779b5801a2c6cc0"
      }
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "encrypted the archive using my telegram bot",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:23:37.380000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511389836890603671",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  },
  {
    "type": 0,
    "content": "wait wait there's more",
    "mentions": [],
    "mention_roles": [],
    "attachments": [],
    "embeds": [],
    "timestamp": "2026-06-02T15:23:20.035000+00:00",
    "edited_timestamp": null,
    "flags": 0,
    "components": [],
    "id": "1511389764140536019",
    "channel_id": "1506332128042811447",
    "author": {
      "id": "1506320109210173460",
      "username": "not_hacker_man",
      "avatar": null,
      "discriminator": "0",
      "public_flags": 0,
      "flags": 0,
      "banner": null,
      "accent_color": null,
      "global_name": "hackerman",
      "avatar_decoration_data": null,
      "collectibles": null,
      "display_name_styles": null,
      "banner_color": null,
      "clan": null,
      "primary_guild": null
    },
    "pinned": false,
    "mention_everyone": false,
    "tts": false
  }
]

```

từ đay ta tìm dc thông tin về bot telegram và key để lấy passwd giải nén file 7z&#x20;

```
    "content": "people send the word “gime” to @helper123_app_bot and it returns the password",
```

<figure><img src="../.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1039).png" alt=""><figcaption></figcaption></figure>

