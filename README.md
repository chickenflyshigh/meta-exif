# meta-exif
Organises media into audio, photo and video categories from the account data downloaded from Facebook and Instagram, and automatically adds relevant EXIF data retrieved from the JSON files into the media. Useful for timelines in photo backup apps and etc.

Main tools required: unzip, ffmpeg, jq, sed, parallel, exiftool.

For Facebook:
1. Download JSON information facebook from [Facebook Accounts Center](https://accountscenter.facebook.com/info_and_permissions).
2. Unzip the files into a directory. E.g. `mkdir facebook && find <downloads directory> -maxdepth 1 -name "facebook-*.zip" -exec unzip '{}' -d facebook`.
3. Create output directory `mkdir facebook_media`
4. Run `./main.sh -d facebook -O facebook_media`.

For Instagram:
1. Download the JSON Instagram information from [Facebook Accounts Center](https://accountscenter.facebook.com/info_and_permissions). 
2. Repeat the remaining steps above, replacing the words facebook with instagram.
