## How Exactly git stores data internally :
    - internally git is a "<key,  value>" datastore.
    - [ key ] :
        . Hash of the data we want to store.
        . 40-digit hexadecimal value.
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