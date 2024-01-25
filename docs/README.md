### Summary
In the DuckDB database, there is a feature for writing custom extensions. Attackers can write a custom extension containing malicious commands based on the DuckDB extension template(https://github.com/duckdb/extension-template). While compiling this malicious extension, a duckdb binary program containing this malicious extension will also be generated. Attackers can replace the normal duckdb program on the victim's machine with this malicious extension. When the victim starts this malicious duckdb program and executes a specific SQL statement, the malicious command will be triggered.

### Impact
https://github.com/duckdb/duckdb，<=0.9.2

# Install vcpkg
```shell
git clone https://github.com/Microsoft/vcpkg.git
./vcpkg/bootstrap-vcpkg.sh
export VCPKG_TOOLCHAIN_PATH=`pwd`/vcpkg/scripts/buildsystems/vcpkg.cmake
```

## Clone the malicious extension
```shell
git clone --recurse-submodules https://github.com/Tu0Laj1/database_test.git
cd database_test
```

### Modify file
Modify src/quark_extension.cpp,modify the file content to a custom malicious command,and modify the extension function name
```sh
vim src/quack_extension.cpp
```
<img width="422" alt="企业微信截图_17032404145324" src="https://github.com/Tu0Laj1/database_test/assets/154296962/c727d9db-3acd-4bde-b92b-cc0c7ce07bcb">

### Build the malicious extension
Now to build the malicious extension, run:
```sh
make
```

## Running the malicious duckdb
/database_test/build/release/duckdb, a malicious duckdb containing malicious extension programs, can be copied and run on any machine. Attackers can replace the victim's normal duckdb with this malicious duckdb
```sh
cd database_test
./build/release/duckdb
```

### Running the malicious extension
When the victim runs a malicious duckdb and the executed SQL statement contains the function name defined by the attacker, the malicious command will be executed. (Why did I choose 'database'? Because 'database/databases' is a function included in many other databases, but it is not available in duckdb, so the probability of victims mistakenly triggering malicious commands will be increased.)
```sh
select database();
```
<img width="465" alt="企业微信截图_1703465596446" src="https://github.com/Tu0Laj1/database_test/assets/154296962/3c1ecb9c-f531-4355-b8e4-be908ac19179">

Of course, when you place this duckdb containing malicious extensions on a Linux host to run, you may need to install additional dependency components, such as GLIBC, etc
