# DH_Assignment
Assignment for DH Class 2026

Documentation (Meret):

Imprint: 23 records without data for the imprint column

Dividing temporal and spatial information,dividing between professional usage and general public use, creating four columns: imprint_dates_final_gp, imprint_dates_final_expert, publisher_final_gp, publisher_final_expert

_______________

Dates:

imprint_dates based on imprint: value.replace(/^.*?([0-9]{4}).*$/, "$1") 

imprint_date find() bsed on imprint: value.replace(/^.*?(\d{4}).*$/, "$1") creates a column with the first 4-digit number that is found, stripped of all punctuation

imprint_dates find() based on imprint: value.find(/[0-9]{4}/).join("; "): find any 4-digit number in the record, join multiple with .join(“; “)

dates_uncertainty based on imprint: value.find(/\[?[0-9]{2,4}\??(?:\s*[-–—]\]?\s*[0-9]{2,4}\??)?[.?]?\]?\??/).join("; "): find any 2- to 4-digit number and preserve square brackets, question marks and periods, does delete many two digit numbers after four digit numbers

dates_uncertainty_clean based on dates_uncertainty: value.replace("[", "").replace("]", "").replace(".", ""): removes all square brackets and periods from dates_uncertainty (preserving question marks), still problem with preserving date ranges in the XXXX-XX format

short_date_test based on imprint : value.find(/\b[0-9]{1,3}\b/).join("; "): searches for any 1- to 3-digit number to check which belong into the date column, this column is used extensively to check the other columns: did any numbers shorter than four digits occur in the original column? Is it preserved in the newly created column?

imprint_dates_punctuation based on imprint: value.find(/\[?[0-9]{2,4}(?:[-–—,]\]?[0-9]{2,4})?[.?]?\]?/).join("; "): attempts to separate the dates while also preserving punctuation

imprint_dates_in_brackets based on imprint:: value.replace(/^(.+?)\,\s+.*/,"1"): attempts to extract all dates within brackets, not much new information gained and put aside 

Imprint_dates_punctuation_2 based on column imprint_dates_punctuation: value.replace(/\b[0-9]{3}\b/, "") as all three digit numbers appearing during testing were not part of any date, they are removed

Imprint_dates_final_expert based on column imprint_dates_punctuation_2: value.replace(/([0-9]{4})[^0-9]+([0-9]{2})([^0-9]|)/,"1-23") creates a hyphen between a 4-digit number and a following 4- or 2-digit number

manual cleaning: checking square brackets, punctuation, if any non-date information is left in the column  

search Imprint_dates_final_expert: ^[^\[]*] are there any square brackets that are not ‘closed’?

search Imprint_dates_final_expert: if(value.split("[")[0].contains("]"), "[" + value, value): in transform, adds an opening bracket to all unmatched closing brackets. Problem: opening brackets at the very start of the entry get duplicated

value.replace(/^\[\[/, "["): finds the duplicated start brackets and returns one start bracket

standard transformations on column Imprint_dates_final_expert: value.replace(/\./, ""): removes all periods, value.replace(/[\p{Zs}\s]+/,' '): removes doubled white space, if(isBlank(value), null, value): turns empty fields into NULL

imprint_dates_final_gp based on Imprint_dates_final_expert: value.replace("[", "").replace("]", "").replace("?", ""): remove uncertainties for general public

______________

Location:

publisher_version_1 based on imprint: value.replace(/^(.*),.*$/, "$1"): remove everything after the last comma, which mostly contains the date information extracted in the previous date column creation

Transform cells in publisher_version_1: if(value.length() - value.replace("[", "").length() > value.length() - value.replace("]", "").length(),value + "]",value): Add closing brackets to the entries with only a opening bracket present, often removed in the splitting process   

publisher_version_2 based on publisher_version_2: value.replace(/^(.*),.*[0-9].*$/,"$1"): remove everything after the last comma if a digit is present, most commas are still in front of time information

publisher_no_date based on publisher_version_2: if(value.replace(/.*[0-9]{4}.*/, "FOUND") == "FOUND", null, value): after some manual cleaning, finds all 4-digit numbers and removes all data from the entry, later set to NULL,as the only information in the entry is the date which is captured in the date column

Manually go through clustering, Key collision, function fingerprint, keeping square brackets and question marks that indicate later imput and uncertainty

creating two columns to keep publisher_final_gp and publisher_final_expert: one with all extracted punctuation of the previous steps for professionals and one with the punctuation removed
