# Closor, Super key and Candidate key



## Algorithm for closor

'''cpp
         C+ = C;
        while (there is changes to C+)
        {
        do (for each functional dependency X->Y in F)
            {
            if (X⊆C+)
                    then C+= C+∪Y
            }
        }
'''
