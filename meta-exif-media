#!/bin/bash

set -e

usage() { echo "Usage: $0 [-d <input directory containing your_facebook_activity etc>] [-O <output directory >]" 1>&2; exit 1; }

while getopts ":O:d:" o; do
    case "${o}" in
        O)
            O="${OPTARG}" 
           ( [ -d "${O}" ] && echo "No output directory specified, outputting in the directory specified by -d flag.") || (echo "Not a directory ${d}" && usage )
        ;;
        d) d="${OPTARG}"
            [ -d "${d}" ] || (echo "Not a directory ${d}" && usage)
        ;;
        *) usage
        ;;
    esac
done

if [ -z "$d" ]; then
    echo "Missing -d." >&2
    usage && exit 1
fi

if [ -z "$O" ]; then
    O="$d"
fi

# cleaning pathnames
O="${O%/}" 
d="${d%/}"
! [ -f "$d/metadata.txt" ] || (echo "metadata.txt already exists in the input directory, exiting for now." && exit 1)


if [ -d "${d}/your_instagram_activity" ] || [ -d "${d}/your_facebook_activity" ]; then
    m="$(find "${d}" -maxdepth 1 -name "your_*_activity" -exec basename '{}' ';' | cut -d '_' -f 2)"
else
    echo "Please ensure the input directory specified by -d contains the \"your_{platform}_activity\" folder." && usage 
fi

echo "Wrapping aac files in mp4 containers. Placing it in the same directory as the original file."

fd '' -e 'aac' "$d" | parallel -n 1 -P $(nproc) 'filename={} && ffmpeg -i ${filename: -4}.aac -c:a copy ${filename: -4}.mp4'

echo "aac to mp4 conversion complete. Proceeding to create folders under $d/exif_media."
mkdir -p "$O/exif_media/"{audio,photo,video}
echo "Folders successfully created."
echo "Extracting information and metadata from all media and outputting into \"${d}/metadata.txt\" file."
for folder in inbox e2ee_cutover archived_threads filtered_threads;
    do 
        if [ -d "${d}/your_${m}_activity/messages/$folder" ]; then
            echo "$(dirname "$0")/smmdo" "$m" "$folder" "${d}/metadata.txt"
            "$(dirname "$0")/smmdo" "$d" "$m" "$folder" "${d}/metadata.txt"
        fi
done

echo "Adding metadata to media files and outputting the editted files in \"$O/exif_media\"."
"$(dirname "$0")/edit_exif_move_media" -f "${d}/metadata.txt" -a "$O/exif_media/audio" -v "$O/exif_media/video" -p "$O/exif_media/photo"

echo "EXIF data successfully added and outputted in \"$O/exif_media\"."
