---
title: YARA規則基礎
date: 2025-05-30 22:08:04
tags:
categories:
  - linux
---

    rule AdvancedMaliciousUPXExecutable {
        meta:
            description = "pico"

        strings:
            $upx1 = "UPX0" ascii
            $upx2 = "UPX1" ascii
            $upx3 = "UPX!" ascii


            $mal_str1 = "Cr/p0" ascii wide
            $mal_str2 = "3'+8U" ascii wide
            $mal_str3 = "CHjM5" ascii wide
            $mal_str4 = "t%j@" ascii wide
            $mal_str5 = "l@Y2" ascii wide
            $mal_str6 = "b3`5d5l5" ascii wide
            $mal_str7 = "NtQueryInformationProcess" ascii wide
            // $mal_str8 = "CreateProcessA" ascii wide
            // $mal_str9 = "TerminateProcess" ascii wide
            $mal_str10 = "DebugActiveProcess" ascii wide
            
            //針對API
            $api_critical1 = "CreateToolhelp32Snapshot" ascii
            $api_critical2 = "Process32FirstW" ascii
            $api_critical3 = "Process32NextW" ascii
            $api_critical4 = "VirtualAllocEx" ascii
            $api_critical5 = "WriteProcessMemory" ascii
            $api_critical6 = "SetThreadContext" ascii
            $api_critical7 = "ResumeThread" ascii

            //針對16進制
            $hex1 = { 25 64 64 64 6C 21 30 } // %dddl!0
            $hex2 = { 62 33 60 35 64 35 6C 35 } // b3`5d5l5
            $hex3 = { 6E 74 51 75 65 72 79 49 6E 66 6F 72 6D 61 74 69 6F 6E 50 72 6F 63 65 73 73 } // NtQueryInformationProcess
            
            // 針對特定名稱
            $sect1 = ".text" ascii
            $sect2 = ".rdata" ascii
            $sect3 = ".data" ascii
            $sect4 = ".rsrc" ascii
            $sect5 = ".reloc" ascii
            $sect6 = ".upx" ascii

        condition:
            uint16(0) == 0x5A4D and  // MZ 頭
            filesize < 100MB and  // 檔案小於100MB
            (

                (
                    (all of ($api_critical*)) and 
                    (1 of ($sect*))
                ) or

                (
                    (1 of ($upx*) and 1 of ($mal_str*))
                ) or


                (
                    2 of ($mal_str*)
                ) or

                (
                    1 of ($hex*)
                )
            )
    }
