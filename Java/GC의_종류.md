# π **GCμ μ’λ₯**

μ΄μ κΈ :  <a href = "https://github.com/yeoonjae/TIL/blob/main/Java/GC.md">GC μ΄ν΄νκΈ°</a>

<hr>
μ΄λ² κΈμμλ GCμ μ’λ₯μ λν΄μ μμλ³΄κ² μ΅λλ€. 
<br>

## π **Serial GC**

* mark-compact μκ³ λ¦¬μ¦μ μ¬μ©
* GC λͺ¨λ μ§λ ¬λ‘ μν(λ¨μΌ κ°μ CPU μ¬μ©) - μ±κΈ μ°λ λ μ¬μ©
* stop-the-world μκ°μ΄ κΉ
* κ°μ₯ μ€λλ GC

* * * 
## π **Parallel GC**

* Mark-Sweep-Compaction μκ³ λ¦¬μ¦μ μ¬μ©
* Java 8μ default GC
* Young μμ­μμ λ©ν° μ°λ λλ₯Ό μ¬μ©νμ¬ GCλ₯Ό μν
* stop-the-world μκ°μ΄ Serial GCμ λΉν΄ κ°μ

* * * 
## π **Parallel Old GC**

* Mark-Summary-Compaction μκ³ λ¦¬μ¦μ μ¬μ©
    * Sweep : λ¨μΌ μ°λ λκ° old μμ­ μ μ²΄λ₯Ό νλλ€.
    * Summary : λ©ν° μ°λ λκ° old μμ­μ λΆλ¦¬ν΄μ νλλ€. 
* Old μμ­λ λ©ν° μ°λ λλ₯Ό μ¬μ©νμ¬ GCλ₯Ό μν

* * * 
## π **CMS(Concurrent Mark Sweep) GC**

* Stop-the-world μκ°μ μ΅μν νκΈ° μν΄ κ³ μλ GC
* CMSμ μμ§ λ¨κ³λ λ€μκ³Ό κ°μ΅λλ€.
    1. Initial Mark (`Stop the World Event`)
        * GC Root μμ μ°Έμ‘°νλ κ°μ±λ€λ§ μ°μ  νμνλ©°, μ°Έμ‘° μ¬λΆλ₯Ό νμνλ€. 
        * μ κ³Όμ μμ Stop-the-worldκ° λ°μνμ§λ§, Stop-the-world μκ°μ΄ μ§§λ€. 
    2. Concurrent Marking
        * Initial Mark κ³Όμ μμ GC μ λμμΌλ‘ νλ³ν κ°μ²΄λ€μ λ°λΌκ°λ©° GCμ λμμΈμ§ μΆκ°λ‘ νμΈνλ€.
        * Stop-the-worldκ° λ°μνμ§ μλλ€. 
    3. Remark (`Stop the World Event`)
        * Concurrent Marking λ¨κ³μ κ²°κ³Όλ₯Ό κ²μ¦νλ€. 
        * μ°λ λμ μλ°μ΄νΈλ‘ μΈν΄ λλ½λ κ°μ²΄κ° μλμ§, μ°Έμ‘°κ° μ κ±°λμλμ§ νμΈνλ€.
        * Stop-the-world κ° λ°μνλ©°, μ΄ μκ°μ μ΅λν μ€μ΄κΈ° μν΄ λ©ν°μ€λ λλ‘ κ²μ¦μμμ μννλ€.
    4. Concurrent Sweep
        * GC μ λμμΌλ‘ μλ³λ κ°μ²΄λ₯Ό λͺ¨λ μ κ±°ν©λλ€. 
        * Stop-the-world κ° λ°μνμ§ μλλ€. 
    5. Resetting
        * λ°μ΄ν° κ΅¬μ‘°λ₯Ό μ§μλ€μ λμ μμ§μ μ€λΉν©λλ€. 

* μμ κ°μ κ³Όμ μ κ±°μΉλ€ λ³΄λ, CPU λΆνκ° μ»€μ§λ λ¨μ μ΄ μ‘΄μ¬ν©λλ€.
* Compaction μ΄ κΈ°λ³Έμ μΌλ‘ μ κ³΅λμ§ μκ³ , νμν  λλ§ μΌμ΄λμ Stop-the-world μκ°μ΄ λ€λ₯Έ GCλ³΄λ€ λ κΈΈκ² κ±Έλ¦΄ μλ μμ΅λλ€. 

* * * 
## π **G1 GC (Garbage First)**
* G1 GC λ λμ©λ λ©λͺ¨λ¦¬κ° μλ λ€μ€ νλ‘μΈμ μμ€νμ λμμΌλ‘ νλ GC μλλ€. 
* G1μ΄ Compactionμ΄ μ κ³΅λλ©° Compactionμ΄ νμν λλ§ μΌμ΄λλ CMS μ λ¨μ μ λ³΄μν©λλ€. 
* G1μ CMS μμ§κΈ°λ³΄λ€ μμΈ‘ κ°λ₯ν κ°λΉμ§ μμ§ μΌμ μ€μ§λ₯Ό μ κ³΅νλ©° μ¬μ©μκ° μνλ μΌμ μ€μ§ λμμ μ§μ ν  μ μμ΅λλ€.
* μ΄μ  GCμ G1 GCμ ν κ΅¬μ‘°λ₯Ό μ΄ν΄λ³΄λ©΄ λ€μκ³Ό κ°μ΅λλ€. 

![image](https://user-images.githubusercontent.com/63777714/137593481-249faa01-035d-4716-8ce6-d87f83b4f91b.png)

μ΄μ  GCλ eden,survivor,old μμ­μ΄ λλ μ Έ μμ§λ§, κ³ μ λ ν¬κΈ°λ‘ μ‘΄μ¬νμ§ μμ΅λλ€. λ°λ©΄, G1 GCλ κ³ μ λ ν¬κΈ°λ‘ RegionμΌλ‘ λΆν λ©λλ€. μ¦, μ μ²΄ Heap μμ­μ΄ μλ, Region λ¨μλ‘ νμν©λλ€. <u>μ΄λ λ©λͺ¨λ¦¬ μ¬μ©μ μμ΄ ν° μ μ°μ±μ μ κ³΅</u>ν©λλ€. 

* Garbageλ§ μ‘΄μ¬νλ Regionμ μκ±°ν΄ κ°μΌλ‘μ¨, Garbage FirstλΌκ³  λΆλ¦½λλ€. 
* G1μ νμ κ°λ₯ν κ°μ²΄, μ¦ μ°λ κΈ°λ‘ κ°λ μ°¨ μμ κ°λ₯μ±μ΄ μλ ν μμ­μ μμ§ λ° μμΆ μμμ μ§μ€ν©λλ€. 
* Full GC κ° μΌμ΄λλ©΄ μμ§ λ¨κ³λ λ€μκ³Ό κ°μ΅λλ€. 
    1. Initial Mark
        * Old Regionμ μ‘΄μ¬νλ κ°μ²΄λ€μ΄ μ°Έμ‘°νλ Survivor Regionμ μ°Ύμ΅λλ€. 
        * μ΄ κ³Όμ μμ Stop-The-Worldκ° λ°μνκ² λ©λλ€.
    2. Root Region Scan
        * Initial Mark μμ μ°Ύμ Survivor Regionμμ GC λμ κ°μ²΄λ₯Ό νμν©λλ€.
    3. Concurrent Mark
        * μ μ²΄ Regionμ λν΄ μ€μΊνμ¬, GC λμ κ°μ²΄κ° μ‘΄μ¬νμ§ μλ Regionμ μ΄ν λ¨κ³μμ μ μΈλ©λλ€.
    4. Remark
        * Stop-The-World ν, GC λμμμ μ μΈν  κ°μ²΄λ₯Ό μλ³ν©λλ€.
    5. Cleanup
        * Stop-The-World ν, μ΄μμλ κ°μ²΄κ° κ°μ₯ μ μ Regionμ λν΄μ μ°Έμ‘°λμ§ μλ κ°μ²΄λ₯Ό μ κ±°ν©λλ€.
        * Stop-The-World λλ΄κ³  μμ ν λΉμμ§ Regionμ Freelistμ μΆκ°νμ¬ μ¬μ¬μ©ν©λλ€. 
    6. Copy
        * Root Region Scan λ¨κ³μμ μ°Ύμ GC λμ Regionμ΄μμ§λ§ Cleanup λ¨κ³μμ μ΄μλ¨μ κ°μ²΄λ€μ Available/Unused Regionμ λ³΅μ¬νμ¬ Compaction μμμ μνν©λλ€. 
    
    <br>
        
    ![image](https://user-images.githubusercontent.com/63777714/137594932-e085179c-dd85-4568-947c-71f273ed92bc.png)


* λ€μκ³Ό κ°μ νΉμ± μ€ νλ μ΄μμ΄ μλ κ²½μ° G1μΌλ‘ μ ννλ κ²μ΄ μ’μ΅λλ€.
    * μ μ²΄ GC κΈ°κ°μ΄ λλ¬΄ κΈΈκ±°λ λλ¬΄ μμ£Ό λ°μν©λλ€.
    * κ°μ²΄ ν λΉ λΉμ¨ λλ μΉκ²© λΉμ¨μ ν¬κ² λ€λ¦λλ€.
    * μνμ§ μλ κΈ΄ κ°λΉμ§ μμ§ λλ μμΆ μΌμ μ€μ§(0.5~1μ΄ μ΄μ)

    <br>
    
* * * 
References <br>
* https://velog.io/@hygoogi/%EC%9E%90%EB%B0%94-GC%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C
* https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/G1GettingStarted/index.html
