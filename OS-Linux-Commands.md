# Operation Systems: Basic Linux/UNIX Commands
    
## Help 

- `man command`: present the manual help file of *command*
  - `man /<strings>`: search for the position of *strings* in the manual file  

- `.`: current position  
- `..`: one level from the current position  


## Directories  

- `pwd`: print working directory   

- `cd dirname/`: go to *dirname*...   
  - `cd ..`: return to the one level up from the current position
  - `cd -`: return to the previous position 

- `mkdir dirname`: make a new directory called *dirname*

- `rm -r dirname/`: recursively delete all files and folders under *dirname*
  - `rm -rf dirname/`: delete without asking for confirmation

- `tree`: present the hierarchical structure under the current directory


## Files

- `ls`: list your files under the current directory  
  - `ls -l`: list your files in 'long format' 
  - `ls -a`: list all your files, including the ones whose filenames begin in a dot (hidden files)  
  - `ls dirname/`: list files under *dirname*
  - `ls -1  | wc -l`: count files under the current directory, including hidden files (`.` and `..`)

- `re + (tab)`: supplement filename starting with *re* with TAB buttom

- `rm filename/`: delete file with *filename* (see above)
  - `rm -i filename/`: require to ask for confirmation before deleting <file> 
  - `rm -f filename/`: attempt to remove the files without prompting for confirmation

- `touch hi.txt`: create <hi.txt> 

- `cat filename`: concatenate, read *filename*

- `vi filename`: edit *filename* using VIM
  - press `i` to start editing file
  - press `(esc) + :wq` to exit editing with changes saved  
  - press `(esc) + :q` to exit editing without saving changes 

- `du`: report the size of files and folders
  - `du -d 1 -h`: summarize by folders only
  - `du -d 1 -h . | sort -h`: sort the folders by size
  
- File compression (tar)
  - `tar -zcvf file.tar.gz /path/to/filename`: compress a single file 
  - `tar -zcvf file.tar.gz /path/to/dir/`: compress an entire *dir* into *file.tar.gz*
  - `tar -zcvf file.tar.gz dir1 dir2 dir3`: compress multiple directories


- File transfer
  - General pattern: `rsync [options] source_file_or_directory destination_directory`, where [options] can be: -avPL
  - To transfer files from remote to local: `rsync -avPL yourUsername@remotehost:/path/to/dir/*pattern* localPath/` 
  - To transfer files from local to remote: `rsync -avPL localPath/*pattern* yourUsername@remotehost:/path/to/dir/`
  - If `*pattern*` includes signs like `*` or `$`, then `path/to/dir/*pattern*` should be quoted. For example: `rsync -avPL yourUsername@remotehost:"/path/to/dir/*.tar.gz" localPath/`

**My commands:**  
`rsync -avPL myuan@gscjump:"~/projects/..." ~/Documents/research/...`

--------------------------

### TBC...
