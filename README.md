# Album-Compress-Helper
A tool to compress album using ffmpeg and exiftool, support photo and video.

Execute ffmpeg to selected file types in source directory recursively, then use ExifTool to copy metadata from original file to new ones.Compressed version is saved in destination directory while keep original file structure.

I use this tool to create a compressed album to save icloud storage usage.

# Requirements
* [.Net 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* [FFMPEG](https://www.ffmpeg.org/) binary executable in system path
* [ExifTool](https://exiftool.org/) binary executable in system path

# Arguments
| Argument Name | Short Name | HelpText                                                                                                                                                                         |
|---------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| src           |            | Path to source directory.                                                                                                                                                        |
| dst           |            | Path to destination directory, create if not exist.                                                                                                                              |
| argff         |            | Arguments* pass to FFMPEG, use %in% for input file path, %out% for output file path                                                                                               |
| argexif       |            | Arguments* pass to ExifTool, use %in% for input file path, %out% for output file path                                                                                             |
| comment       | c          | Ignore file with spicific comment tag from input, otherwise write comment to output file.                                                                                        |
| ignore        | i          | Ignore if files already exist in destination.                                                                                                                                    |
| ext           |            | File extensions filter such as jpg or mp4, use',' to seperate multiple extensions.                                                                                               |
| thread        | t          | Set multi-thread limit, default is 1.                                                                                                                                            |
| date          | d          | Modify lastwrite and creation time, available options: copy(copy from original), min or max(select minimum or maximum datetime, then write to both lastwrite and creation time) |
| keep          | k          | Copy original file if compressed version is larger. |
| verbose       | v          | Show verbose info.                                                                                                                                                               |
| vvv           |            | Show more verbose info, mostly from ffmpeg.                                                                                                                                      |

* Due to CommandLineParser, arguments pass to ffmpeg and ExifTool start with dash(-) need escape character '\\' in the beginning.

# Example Usage
## Compress jpeg,jpg,png

    Album-Compress-Helper --src "C:\SrcFolder" --dst "C:\DstFolder" --ext jpeg,jpg,png --argff "\-i %in% \-q:v 3 %out%" --argexif "\-TagsFromFile %in% \-overwrite_original \-all:all>all:all %out%" -k -i -c compressed -d min -t 4

| Argument Name | Value                                                                 | Description                                                                                                                                                                                   |
|---------------|-----------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| src           | "C:\SrcFolder"                                                        | Path to source directory.                                                                                                                                                                     |
| dst           | "C:\DstFolder"                                                        | Path to destination directory, create if not exist.                                                                                                                                           |
| ext           | jpeg,jpg,png                                                          | Apply to *.jpeg *.jpg and *.png files.                                                                                                                                                        |
| argff         | "\\-i %in% \\-q:v 3 %out%"                                            | will be translate to shell command "ffmpeg -i C:\SrcFolder\\*files.*ext -q:v 3 C:\DstFolder\\*files.*ext" and execute, compress files with -q:v 3 quality setting.                              |
| argexif       | "\\-TagsFromFile %in% \\-overwrite_original \\-all:all>all:all %out%" | will be translate to shell command "exiftool -TagsFromFile C:\SrcFolder\\*files.*ext -overwrite_original -all:all>all:all C:\DstFolder\\*files.*ext" and execute, copy tags from original file. |
| keep          | true                                                                  | Copy original file if compressed version is larger.                                                                                                                                           |
| ignore        | true                                                                  | Ignore if files already exist in destination.                                                                                                                                                 |
| comment       | compressed                                                            | Ignore file with comment value 'compressed', otherwise write 'compressed' comment to output file.                                                                                             |
| date          | min                                                                   | Modify lastwrite and creation time, select the minimum value and write to both.                                                                                                               |
| thread        | 4                                                                     | Run 4 thread simultaneously.                                                                                                                                                                  |

## Compress avi,mp4,mov

    Album-Compress-Helper --src "C:\SrcFolder" --dst "C:\DstFolder" --ext avi,mp4,mov --argff "\-i %in% \-preset:v medium \-c:v libx264 \-crf 24 %out%" --argexif "\-TagsFromFile %in% \-overwrite_original \-all:all>all:all %out%" -i -c compressed -d min --vvv

| Argument Name | Value                                                                 | Description                                                                                                                                                                                                                      |
|---------------|-----------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| src           | "C:\SrcFolder"                                                        | Path to source directory.                                                                                                                                                                                                        |
| dst           | "C:\DstFolder"                                                        | Path to destination directory, create if not exist.                                                                                                                                                                              |
| ext           | avi,mp4,mov                                                           | Apply to *.avi *.mp4 and *.mov files.                                                                                                                                                                                            |
| argff         | "\\-i %in% \\-preset:v medium \\-c:v libx264 \\-crf 24 %out%"         | will be translate to shell command "ffmpeg -i C:\SrcFolder\\*files.*ext -preset:v medium -c:v libx264 -crf 24 C:\DstFolder\\*files.*ext" and execute, compress video with "-preset:v medium -c:v libx264 -crf 24" quality setting. |
| argexif       | "\\-TagsFromFile %in% \\-overwrite_original \\-all:all>all:all %out%" | will be translate to shell command "exiftool -TagsFromFile C:\SrcFolder\\*files.*ext -overwrite_original -all:all>all:all C:\DstFolder\\*files.*ext" and execute, copy tags from original file.                                    |
| keep          | true                                                                  | Copy original file if compressed version is larger.                                                                                                                                                                              |
| ignore        | true                                                                  | Ignore if files already exist in destination.                                                                                                                                                                                    |
| comment       | compressed                                                            | Ignore file with comment value 'compressed', otherwise write 'compressed' comment to output file.                                                                                                                                |
| date          | min                                                                   | Modify lastwrite and creation time, select the minimum value and write to both.                                                                                                                                                  |
| vvv           | true                                                                   | Show info from ffmpeg.                                                                                                                                                                                                           |

# Package List
* [CommandLineParser](https://github.com/commandlineparser/commandline)
