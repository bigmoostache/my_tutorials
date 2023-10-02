On Ubuntu, you can use `aria2` to download torrents from the command line. Here's a step-by-step guide:

1. **Install aria2**: 

   If you haven't installed `aria2`, you can do so using the package manager:

   ```bash
   sudo apt update
   sudo apt install aria2
   ```

2. **Download a Torrent**:

   To download a torrent file:

   ```bash
   aria2c /path/to/torrent/file.torrent
   ```

   If you have a magnet link, you can also use `aria2` to download:

   ```bash
   aria2c "magnet:?xt=urn:btih:YOUR_MAGNET_LINK_HERE"
   ```

3. **Additional Options**:

   `aria2` has a lot of options to customize your download experience. A few notable ones are:

   - `--seed-time=0`: By default, `aria2` will seed downloaded torrents. Set the seed time to 0 if you don't wish to seed after downloading.
   - `--max-upload-limit=0`: Set the maximum upload speed to 0 if you don't want to upload at all.
   - `-d /path/to/download/directory`: To specify a directory for the downloaded files.

   For example:

   ```bash
   aria2c --seed-time=0 -d /home/user/Downloads /path/to/torrent/file.torrent
   ```

4. **Manage Downloads**:

   Once you've started a download with `aria2`, you can pause/resume or stop the download using the appropriate keyboard shortcuts:

   - `Ctrl+C`: Stop the download.
   - `Ctrl+D`: Pause the download.
   - `Ctrl+K`: Resume a paused download.

Remember, always ensure that you have the rights to download and distribute the content you're getting via torrents, as there could be legal consequences for downloading copyrighted content without the appropriate permissions.