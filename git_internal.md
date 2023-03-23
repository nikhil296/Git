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
    - It is recommended to check the type and content of object in .git folder
      after commiting the change.
    - " git cat-file <flag> <hash (5-6 chars only)> "
    - <flag> :
        . ' -t ' = tells type of object,  
        . ' -p ' = prints content of object.

eg :- 
    `git cat-file -t 10b20`     = "blob"
    `git cat-file -p 10b20`     = "console.log("Hi there!");"

eg :-
    `git cat-file -t cbdf7`     = "blob"
    `git cat-file -p cbdf7`     = "console.log("I am Nikhil");"

eg :-
    `git cat-file -t e69de`     = "blob"
    `git cat-file -p e69de`     = ""

eg :-
    `git cat-file -t f95c7`     = "blob"
    `git cat-file -p f95c7`     = "console.log("Hi there");" 

</details>

### `Commit` in git is a object :-
<details>
<summary>Details : </summary>
    - commit in git is alaso an object like tree and blob.
    - every commit points to a tree.
    - The commit object has data of the 
        1. author & commmiter,
        2. date of commit,
        3. message,
        4. Parent commit.
        
-    before commit                                           after commit
-   .git                                                    .git
    ├── HEAD                                                ├── COMMIT_EDITMSG
    ├── config                                              ├── HEAD
    ├── description                                         ├── config
    ├── index                                               ├── description
    ├── info                                                ├── index        
    │   └── exclude                                         ├── info                                
    ├── objects                                             |   └── exclude                    
    │   ├── 10                                              ├── logs               
    │   │   └── b20aa995a4e19d19cc3a5314802ac96f87696d      │   ├── HEAD                            
    │   ├── 4b                                              │   └── refs                    
    │   │   └── 0a5bc78e3be70b820bfc7249f7c0200ac5cd94      |       └── heads   
    │   ├── 9a                                              |            └── main        
    │   │   └── 455b676e4880aeee03043a3113e09eb8519cd7      ├── objects
    │   ├── cb                                              │   ├── 10
    │   │   └── df7e4bb92d146367826585642517dca6c890f8      │   │   └── b20aa995a4e19d19cc3a5314802ac96f87696d
    │   ├── d1                                              │   ├── 13                    
    │   │   └── ce06f644d2ca5ca7ecc124bc2879fc058ddfa1      │   │   └── eef8fc790f3fe70b9ccbda6ec41b81386322b0 
    │   ├── e6                                              │   ├── 4b 
    │   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391      │   │   └── 0a5bc78e3be70b820bfc7249f7c0200ac5cd94 
    │   ├── f9                                              │   ├── 7e 
    │   │   └── 5c728a57f81e55493290e35d0fa0fc0f185cc7      │   │   └── fb1a4176760219b281c527baa2ba692d2ac19a 
    │   ├── info                                            │   ├── 9a 
    │   └── pack                                            │   │   └── 455b676e4880aeee03043a3113e09eb8519cd7 
    └── refs                                                │   ├── bb 
        ├── heads                                           │   │   └── a717de1d23f6994853c15246c8f723a4f42914 
        └── tags                                            │   ├── cb 
                                                            │   │   └── df7e4bb92d146367826585642517dca6c890f8
                                                            │   ├── d1
                                                            │   │   └── ce06f644d2ca5ca7ecc124bc2879fc058ddfa1
                                                            │   ├── e6
                                                            │   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
                                                            │   ├── f2
                                                            │   │   └── c42ca21dfa420bb1de6b75fb2c06be3eb8bfdb
                                                            │   ├── f9
                                                            │   │   └── 5c728a57f81e55493290e35d0fa0fc0f185cc7
                                                            │   ├── info
                                                            │   └── pack
                                                            └── refs
                                                                ├── heads
                                                                │   └── main
                                                                └── tags

    - git cat-file -t 13ee  =  "commit"
    - git cat-file -p 13ee  =  'point to the root directory (f2c4 ...)'
        tree f2c42ca21dfa420bb1de6b75fb2c06be3eb8bfdb
        author nikhil296 <nikhilgautam1729@gmail.com> 1679569570 +0530
        committer nikhil296 <nikhilgautam1729@gmail.com> 1679569570 +0530

        First Commit

    - git cat-file -t f2c4    =   "tree"
    - git cat-file -p f2c4
        040000 tree bba717de1d23f6994853c15246c8f723a4f42914	coding
        040000 tree 7efb1a4176760219b281c527baa2ba692d2ac19a	nothing
        100644 blob 10b20aa995a4e19d19cc3a5314802ac96f87696d	test.js
        100644 blob 10b20aa995a4e19d19cc3a5314802ac96f87696d	test1.js
        100644 blob cbdf7e4bb92d146367826585642517dca6c890f8	test2.js
        100644 blob f95c728a57f81e55493290e35d0fa0fc0f185cc7	test3.js
        100644 blob 10b20aa995a4e19d19cc3a5314802ac96f87696d	test4.js
        100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391	test5.js

    - git cat-file -t 7efb     =   "tree"
    - git cat-file -p 7efb     =   "point to the 'nothing' directory"
        100644 blob d1ce06f644d2ca5ca7ecc124bc2879fc058ddfa1	index.js

    - git cat-file -t bba71     =   "tree"
    - git cat-file -p bba71     =   "points to the 'coding' directory"
        100644 blob 4b0a5bc78e3be70b820bfc7249f7c0200ac5cd94	testing.js
        100644 blob 9a455b676e4880aeee03043a3113e09eb8519cd7	testing2.js

    - now if we make another commit, then the second commit will point to the first commit (or parent commit).
    - eg :
        git cat-file -t 21f2    =   "commit"
        git cat-file -p 21f2    =   "points to the root directory(b7dbb (this is different from previous root as there were changes so new hash is generated)) and the parent ("first commit" - 13eef)
        tree b7dbbcd749ccfa86d7eb8b73af842ae20a2c76ae
        parent 13eef8fc790f3fe70b9ccbda6ec41b81386322b0
        author nikhil296 <nikhilgautam1729@gmail.com> 1679575224 +0530
        committer nikhil296 <nikhilgautam1729@gmail.com> 1679575224 +0530

        second commit



                                                        
                                                        
                                                        
                                                        
                                                        
                                                        
                                                        
                                                        





eg :- 
    `git cat-file -t 10b20`     = "blob"
    `git cat-file -p 10b20`     = "console.log("Hi there!");"

eg :-
    `git cat-file -t cbdf7`     = "blob"
    `git cat-file -p cbdf7`     = "console.log("I am Nikhil");"

eg :-
    `git cat-file -t e69de`     = "blob"
    `git cat-file -p e69de`     = ""

eg :-
    `git cat-file -t f95c7`     = "blob"
    `git cat-file -p f95c7`     = "console.log("Hi there");" 

</details>