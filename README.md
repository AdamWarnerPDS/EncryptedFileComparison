# EncryptedFileComparison
# How to use the file comparison tools
## Clean server data collection
* Collect the script contents from: https://github.com/AdamWarnerPDS/EncryptedFileComparison/blob/main/Get-FilesAndCheckForEncryptionOrAge.ps1
  * Paste them into a new text file and rename said text file to Get-FilesAndCheckForEncryptionOrAge.ps1
* In powershell run the following:
  * "Get-FilesAndCheckForEncryptionOrAge.ps1 -opMode clean"
* This will scan and process all files from the C: drive except the following directories:
  * Windows, Program Files, Program Files (x86), ProgramData
* It will also ignore files with the suffix $ignoreSuffix (default is *_help*)
* Collect the output from your current directory <hostname>_<datetime>_clean_data_all_.csv

## Infected/Corrupt server
* Attach the vmdk for the corrupt server to a disposable virtual machine
* Mount the disk using Disk Management
  * Make a note of the mounted disk's letter (F: will be used in the following examples)
* Collect the script contents from: https://github.com/AdamWarnerPDS/EncryptedFileComparison/blob/main/Get-FilesAndCheckForEncryptionOrAge.ps1
  * Paste them into a new text file and rename said text file to Get-FilesAndCheckForEncryptionOrAge.ps1
* Run the following in powershell "Get-FilesAndCheckForEncryptionOrAge.ps1 -rootDir 'F:\' -hostnameOverride <the corrupt servers hostname>
* This will scan and process all files from the C: drive except the following directories:
  * Windows, Program Files, Program Files (x86), ProgramData
* It will also ignore files with the suffix $ignoreSuffix (default is *_help*)
* Locate the output from your current directory <the corrupt servers hostname>_<datetime>_infected_data_all_.csv
* Next, we need to clean up the output due to differing drive roots between the clean and infected servers
  * This is necessary for matching later
  * Open the output csv in notepad and perform a find and replace:
    * Find: F:\
    * Replace with: C:\
* Collect the output from your current directory <the corrupt servers hostname>_<datetime>_infected_data_all_.csv

## Processing
* Collect the script contents from: https://github.com/AdamWarnerPDS/Public/blob/master/Get-EncryptedFileComparison.ps1
  * Paste them into a new text file and rename said text file to Get-EncryptedFileComparison.ps1
* In powershell, run the following:
  * "Get-EncryptedFileComparison.ps1 -infectedReport "<path to infected computer output> -cleanReport <path to clean computer report> -hostname <hostname of the computer you're inspecting>
* Collect the output <datetime>_<hostname>_FileComparisonReport.csv

## About the report
### How to use
* Open a blank workbook in excel
* Import the <datetime>_<hostname>_FileComparisonReport.csv file and accept defaults

# Explainations:
* Items with "I_" originated from the infected computer
* Items with "C_" originated from the clean computer
* Items are matched using the I_OrigFullPath and C_FullPath columns
* "I_Encrypted" means that the encrypted suffix was found
* "*_Newer" means that the file is newer than the threshold; default threshold is "10/19/2020 12:00:00 AM"
* You will find what seems to be too many date, time, or datetime columns, these will be useful for sorting.  Hide them if you like
* If a row's "C_*" columns are populated with "empty", this means a matching "I_OrigFullPath" was not found.
  * There is a known bug with the current encrypted suffix (*.3ncrypt3d*) causing issues with .json files and matching
