## How Exactly git stores data internally :
    - internally git is a "<key,  value>" datastore.
    - "key" :
        . Hash of the data we want to store.
        . 40-digit hexadecimal value.
        . for same value, this hash will be same.
            
    - "value" : 
        . actual data.
        . git stores the compressed data in a "blob" and some more metadata in the header.
            - ' blob ' :
                . "binary large object"     or     "big large object"
                . it is a data type to store very large piece of data inside an object.
                .  _________________________________
                  |  blob   |   size of content     |
                  |_________|_______________________|
                  | \0  - 'delimeter'               |
                  |_________________________________|
                  | content of the data             |
                  |_________________________________|