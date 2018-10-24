# TextID

import random
import math
from porter import create_stem

class TextModel:

    def __init__(self, name):
        """ the constructor for the TextModel class
            all dictionaries are started at empty
            the name is just for our own purposes, to keep things 
            organized
        """
        self.name = name
        self.words = {}   # starts empty
        self.wordlengths = {}
        self.stems = {}
        self.sentencelengths = {}
        self.punctuation = {}
        # you will want another dictionary for your text feature


    def __repr__(self):
        """ this method creates the string version of TextModel objects
        """
        s  = "\nModel name: " + str(self.name) + "\n"
        s += "    n. of words: " + str(len(self.words))  + "\n"
        s += "    n. of word lengths: " + str(len(self.wordlengths))  + "\n"
        s += "    n. of sentence lengths: " + str(len(self.sentencelengths))  + "\n"
        s += "    n. of stems: " + str(len(self.stems))  + "\n"
        # you will likely want another line for your custom text-feature!
        return s





    def readTextFromFile(self, filename):
        """should take in a filename (a string) and should return all of the text in that file as a single, very large string
        """

        f = open( filename )
        text = f.read()
        f.close()
        Low = text.split()
        Low= ['$']+ Low
        d={}

        return text

    def makeSentenceLengths(self, s):
        """ This method uses the text in the input string s to create the self.sentencelengths dictionary
        """

        Low = s.split()
        d={}

        punc=['.', '?','!']

        wc= 0
        sc = 0
        for x in range(len(Low)):
            if Low[x][-1] not in punc: 
                wc += 1 

            elif Low[x][-1] in punc:
                sc += 1 
                if wc in d: 
                    d[wc] = d[wc] + 1
                else: 
                    d[wc] = 1
                w = 0
        self.sentencelengths = d
            


    def cleanString(self,s):
        """This method should take in a string s and returns a string with no punctuation and no upper-case letters.
        """
        s = s.lower()
        temp = ""
        punc = ['.', '?', '!', "'", ',']
        for i in range(len(s)):
            if s[i] not in punc:
                temp = temp + s[i]
        return temp 



    def makeWordLengths(self,s): 
        """ This method uses the text in the input string s to create the self.wordlengths dictionary
        """

      
        s = self.cleanString(s)
        Low = s.split() 
        d={}

        for x in range(len(Low)):
            if len(Low[x]) in d: 
                d[len(Low[x])] += 1

            elif len(Low[x]) not in d:
                d[len(Low[x])] = 1
                
        self.wordlengths = d
            
    def makeWords(self,s):
        """ This method uses the text in input string s to create the self.words dictionary
        """
        s = self.cleanString(s)
        Low = s.split()
        d = {}

        for x in range(len(Low)):
            if Low[x] in d:
                d[Low[x]] += 1
            elif Low[x] not in d: 
                d[Low[x]] = 1
        self.words = d

    def makeStems(self,s):
        """ This method uses the text in input string s to create the self.stems dictionary
        """ 
        s = self.cleanString(s)
        Low = s.split()
        d = {}
        for x in range(len(Low)): 
            if create_stem(Low[x]) in d: 
                d[create_stem(Low[x])] += 1 
            elif create_stem(Low[x]) not in d: 
                d[Low[x]] = 1
        self.stems = d


    def makePunctuation(self,s):
        """ This method uses the text in input string s to create the self.makePunctuation dictionary
        """

        
        Low = s.split()
        d = {}
        punc = ['.', '?', '!', "'", ',']

        for x in range(len(s)):
            if s[x] in punc: 
                if s[x] in d: 
                    d[s[x]] += 1
                else: 
                    d[s[x]] = 1
            
        self.punctuation = d              


    def printAllDictionaries(self):
        """This method should print out all five of self's dictionaries in full - for testing and checking how they are working...
        """
        print "The text model named [ Milestone test ] has dictionaries: \n"
        print "self.sentencelengths: " + str(self.sentencelengths) 
        print "\nself.words: " + str(self.words)
        print "\nself.wordlengths: " + str(self.wordlengths)
        print "\nself.stems: " + str(self.stems)
        print "\nself.punctuation: " + str(self.punctuation)        

    def normalizeDictionary(self, d):
        """ The method takes in any single of the model-dictionaries d and returns
        a normalized version (one in which the values adds up to 1.0)
        """

        values = d.values() 
        x = float(sum(values))
        n = {}
        for k in d: #loop through all the keys 
            n[k] = (d[k]/x)
        return n

    def smallestValue(self, nd1, nd2): 
        """ This method takes in any two model-dictionaries nd1 and nd2 
        and returns the smallest positive (non-zero) value across them both
        """

        valuesnd1 = nd1.values()
        valuesnd2 = nd2.values()

        x = min(valuesnd1)
        y = min(valuesnd2)

        if x < y: 
            return x
        else: 
            return y


    def compareDictionaries(self, d, nd1, nd2):
        """ This method computes the log-probability that the dictionary d 
        arose from the distribution of data in the normalized ditionary nd1 
        AND the log-probability that dictionary d arose from the distribution of 
        data in normalized dictionary nd 2 AND returns both the log-probability values 
        """
        nd1 = self.normalizeDictionary(nd1) #clean 'er up
        nd2 = self.normalizeDictionary(nd2) 
        totalnd1 = 0 
        totalnd2 = 0
        epsilon =  (self.smallestValue(nd1,nd2))/2
        for k in d: 
            if k not in nd1: 
                totalnd1 += d[k]*math.log(epsilon)
            else: 
                totalnd1 += d[k]*math.log(nd1[k])
        for k in d: 
            if k not in nd2: 
                totalnd2 += d[k]*math.log(epsilon)
            else: 
                totalnd2 += d[k]*math.log(nd2[k]) 
                                                                     # i think he's cute ;) #here for the bae # what is comp sci but a way to meet boys # what is college but a way to meet your husband 
        YAZZZZQUEEN = [totalnd1, totalnd2]                                                                                      # ^ college style passing notes 
        return YAZZZZQUEEN 

    def createAllDictionaries(self, s): 
        """ should create out all five of self's 
            dictionaries in full - for testing and 
            checking how they are working...
        """
        self.makeSentenceLengths(s)
        new_s = self.cleanString(s)
        self.makeWords(new_s)
        self.makeStems(new_s)
        self.makePunctuation(s)
        self.makeWordLengths(new_s)


    def compareTextWithTwoModels(self,model1,model2):
        """ This method runs the compareDictionaries method for all the 
        feature dictionaries in self against the corresponding (normalized!)
        dictionaries in model1 and model2
        It also prints the comparative results of the log-probabilites for all 
        five dictionaries and implements a system that determines which model self
        is closer to and declare a winner
        """
        
        
        a = self.compareDictionaries(self.words, model1.words, model2.words)
        b = self.compareDictionaries(self.wordlengths, model1.wordlengths, model2.wordlengths)
        c = self.compareDictionaries(self.sentencelengths, model1.sentencelengths, model2.sentencelengths)
        d = self.compareDictionaries(self.stems, model1.stems, model2.stems)
        e = self.compareDictionaries(self.punctuation, model1.punctuation, model2.punctuation)

        print "Overall comparison of unknown_tm vs trained_tm1 and trained_tm2: \n"
        print "name" + " "* 10 + "vs M1" + " "* 10 + "vs M2" + "\n"
        print "---" + " "* 10 + "----" + " "* 10 + "-----" + "\n"
        print "words" + " "* 10 + str(round(a[0],3)) + " " * 10 + str(round(a[1],3)) 
        print "wordlengths" + " " *3+ str(round(b[0],3)) + " " *10 + str(round(b[1],3))
        print "sentencelengths" + " " *2 + str(round(c[0],3)) + " " *8 + str(round(c[1],3))
        print "stems" + " " *10 + str(round(d[0],3)) + " " *10 + str(round(d[1],3))
        print "punctuation" + " "*4 + str(round(e[0],3)) + " "*10 + str(round(e[1],3))
        print "\n"
        print "\n"
        print "\n"
        print "\n"
        print "\n"
        featuresm1 = 0
        featuresm2 = 0
        if abs(a[0]) < abs(a[1]):
            featuresm1 += 1
        else: 
            featuresm2 += 1

        print "[1] Model1 wins on " + str(featuresm1) + "features" + "\n"
        print "[2] Model2 wins on " + str(featuresm2) + "features" + "\n"
        print "\n"
        if featuresm1 > featuresm2: 
            print " ++++++      Model1 is the better match!     ++++++"
        else: 
            print " ++++++      Model2 is the better match!     ++++++" 
# # put into a file named train1.txt
## # put into a file named train1.txt
#This is a small sentence. This isn't a small sentence, because this sentence contains more than 10 words and a number! This isn't a question, is it?

trained_tm1 = TextModel( "Model1" )
text1 = trained_tm1.readTextFromFile( "train1.txt" )
trained_tm1.createAllDictionaries(text1)  # provided in hw description
trained_tm1.printAllDictionaries()

# # put into a file named train2.txt
#Many years later, as he faced the firing squad, Colonel Aureliano Buendia was to remember that distant afternoon when his father took him to discover ice.

trained_tm2 = TextModel( "Model2" )
text2 = trained_tm1.readTextFromFile( "train2.txt" )
trained_tm2.createAllDictionaries(text2)  # provided in hw description
trained_tm2.printAllDictionaries()

# # put into a file named unknown.txt
#This sentence contains fire and ice! Winter is icy, isn't it?

unknown_tm = TextModel( "Unknown (trial)" )
text_unk = unknown_tm.readTextFromFile( "unknown.txt" )
unknown_tm.createAllDictionaries(text_unk)  # provided in hw description
unknown_tm.printAllDictionaries()
#This is a small sentence. This isn't a small sentence, because this sentence contains more than 10 words and a number! This isn't a question, is it?

trained_tm1 = TextModel( "Model1" )
text1 = trained_tm1.readTextFromFile( "train1.txt" )
trained_tm1.createAllDictionaries(text1)  # provided in hw description
trained_tm1.printAllDictionaries()

# # put into a file named train2.txt
#Many years later, as he faced the firing squad, Colonel Aureliano Buendia was to remember that distant afternoon when his father took him to discover ice.

trained_tm2 = TextModel( "Model2" )
text2 = trained_tm1.readTextFromFile( "train2.txt" )
trained_tm2.createAllDictionaries(text2)  # provided in hw description
trained_tm2.printAllDictionaries()

# # put into a file named unknown.txt
#This sentence contains fire and ice! Winter is icy, isn't it?

unknown_tm = TextModel( "Unknown (trial)" )
text_unk = unknown_tm.readTextFromFile( "unknown3.txt" )
unknown_tm.createAllDictionaries(text_unk)  # provided in hw description
unknown_tm.printAllDictionaries()


#
# here is the main comparison method
#   It compares the unknown_tm with two models
#   Here, the two models are trained_tm1 and trained_tm2
#
unknown_tm.compareTextWithTwoModels(trained_tm1,trained_tm2)

# # put the text between these triple-quotes into a file named test.txt
# test_text = """This is a small sentence. This isn't a small sentence, because
# this sentence contains more than 10 words and a number! This isn't
# a question, is it?"""

# test_tm = TextModel( "Milestone test" )

# text = test_tm.readTextFromFile( "test.txt" )
# print "Is text == test_text? ", text == test_text

# # create all of the dictionaries
# test_tm.makeSentenceLengths(text)
# test_tm.makeWordLengths(text)
# test_tm.makeWords(text)
# test_tm.makePunctuation(text)
# test_tm.makeStems(text)

# # first, let's see test_tm via the __repr__ method
# print test_tm

# # next, let's see all of the dictionaries:
# test_tm.printAllDictionaries()
