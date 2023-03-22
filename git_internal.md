# How Exactly git stores data internally :
    - git internally does a lot of optimization.
    - the objects are stored in compressed form.
    - it mainly stores data about the change & algorithmically shows us the file content with 
      that change.
    - even if we delete any files or folder in git repo, the objects in ".git" is not deleted
      cuz, we can anytime rollback to the previous change/version with this feature.

### How git handles files
<details>
<summary>Details : </summary>

    - Files are represented by "blob Object".

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

**NOTE : all the above complex internal structure can be visualized inside the `" .git "` folder.**

### Visualization : 
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

**NOTE  : `inside git content is stored only once`**

### Explanation :
    - if we create a file,
       "test1.js"  ->  { console.log("Hi there!"); }
       next, adding this file to git repo will create a folder inside ".git -> object"
       now,
       if we create another file "test2.js" with same data { console.log("Hi there!"); }
       and
       adding this file to git repor will not create new folder inside the ".git -> object"
       bcz
       git does not store duplicate content.
        .git
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

    - if we create "test3.js" with content { console.log("I am Batman"); }
      and add this to repo, the tree will look like below.
        .git
        ├── HEAD
        ├── config
        ├── description
        ├── index
        ├── info
        │   └── exclude
        ├── objects
        │   ├── 10
        │   │   └── b20aa995a4e19d19cc3a5314802ac96f87696d
        │   ├── e6
        │   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
        │   ├── info
        │   └── pack
        └── refs
            ├── heads
            └── tags
    
    - now, if we change content of "test3.js" to { console.log("Hi there!"); }
        .git
        ├── HEAD
        ├── config
        ├── description
        ├── index
        ├── info
        │   └── exclude
        ├── objects
        │   ├── 10
        │   │   └── b20aa995a4e19d19cc3a5314802ac96f87696d
        │   ├── e6
        │   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
        │   ├── info
        │   └── pack
        └── refs
            ├── heads
            └── tags
        the tree will still look like this, but "test3.js" is now stored in,
               e6
               └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391 
    - now if we create a new file say "test4.js" and content { console.log("I am Batman"); }
        it will be represented by 
               10
               └── b20aa995a4e19d19cc3a5314802ac96f87696d
</details>

### How git handles directories :
<details>
<summary>Details : </summary>

    - Tree :
        . Directories are represented by "Tree Object".
        . It stores information about directories and their content.
        . It contains pointers to other blobs and trees.
        eg :-
                                Tree 
                    ______________|____________
                   |                           |
                  blob                       Tree
                                 ______________|____________
                                |                           |
                               blob                        blob  

</details>

### How to check the content and type of a blob or tree object : 
<details>
<summary>Details : </summary>

    - " git cat-file <flag> <hash (5-6 chars only)> "
    - <flag> :      ' -t ' = tells type of file,  ' -p ' = prints content of file.
    eg :- 
    
*** `git cat-file -t 10b20` ***     = "blob"
*** `git cat-file -p 10b20` ***     = "console.log("Hi there!");"

*** `git cat-file -t cbdf7` ***     = "blob"
*** `git cat-file -p cbdf7` ***     = "console.log("I am Nikhil");"

*** `git cat-file -t e69de` ***     = "blob"
*** `git cat-file -p e69de` ***     = ""

*** `git cat-file -t f95c7` ***     = "blob"
*** `git cat-file -p f95c7` ***     = "console.log("Hi there");"

        . Directories are represented by "Tree Object".
        . It stores information about directories and their content.
        . It contains pointers to other blobs and trees.
        eg :-
                                Tree 
                    ______________|____________
                   |                           |
                  blob                       Tree
                                 ______________|____________
                                |                           |
                               blob                        blob  

</details>