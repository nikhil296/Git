## How Exactly git stores data internally :
    - internally git is a "<key,  value>" datastore.
    - [ key ] :
        . Hash of the data we want to store.
        . 40-digit hexadecimal value. 
        . SHA1 algorithm is used to generate the hash code.
        . for same value, this hash will be same.
            
    - [ value ] : 
        . actual data.
        . git stores the compressed data in a "blob" and some more metadata in the header.
            - ' blob ' :
                . "binary large object"     or     "big large object"
                . it is a data type to store very large piece of data inside an object.
                .  _____________________________________________________________________________
                  |  blob   (identifier like 'x' or 'y')  |           size of content           |
                  |_______________________________________|_____________________________________|
                  |                           '\0'  - 'delimeter'                               |
                  |_____________________________________________________________________________|
                  |                           content of the data                               |
                  |_____________________________________________________________________________|

**NOTE : all the above complex internal structure can be visualized inside the " .git " folder.**

## Visualization : 
    -       .git
            ├── HEAD
            ├── config
            ├── description
            ├── index
            ├── info
            │   └── exclude
            ├── objects
            │   ├── e6
            │   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
            │   ├── info
            │   └── pack
            └── refs
                ├── heads
                └── tags

    - inside the " objects " folder, in the tree above, we actually store the blobs.
    - folders are created inside the "objects" folder.
    - key (40 characters) : 
        . first 2 chars are used to name the directory/folder in which the data is stored.
            eg : " e6 "
        . remaining 38 chars are used to creating a file.
            eg : " 9de29bb2d1d6434b8b29ae775ad8c2e48c5391 "

**###NOTE  : `inside git content is stored only once`**