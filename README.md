# DVC 簡易使用說明

###### tags: `Tools`

###### created by Weber Huang 2022-10-06

## 一、簡介

在進行資料科學專案，訓練模型所需輸入與輸出的<u>資料、模型、模型結果...等</u>檔案不會被 Git 所追蹤，因此若想要藉由 Git 分支與不同版本標籤之間切換進行不同的實驗，容易會有資料與模型不一致問題，因此 DVC 是結合 Git 用來對資料與模型做版本追蹤，資料可以儲存於本地端或是遠端諸如 SSH、gdrive、s3 等地方。

## 二、使用範例

### 1. 新建專案

### 2. 下載現成專案

+ 環境: Python 3.7

+ 請事先[安裝 DVC](https://dvc.org/doc/install)

  +　[Running DVC on Windows](https://dvc.org/doc/user-guide/how-to/running-dvc-on-windows)
    +　[Install with pip](https://dvc.org/doc/install/windows#install-with-pip)

	+ 下載專案環境

	  ```bash
	    $ git clone https://github.com/iterative/example-versioning.git
		  $ cd example-versioning
		    ```

			+ 配置專案環境

			  ```bash
			    $ python3 -m venv .env
				  
				    $ source .env/bin/activate
					  # source .env/Scripts/activate
					    
						  $ pip install -r requirements.txt
						    ```

							+ 取得訓練資料

							  +　`dvc get` 指令用於取得被 dvc 所追蹤的專案模型資料，詳細請見j文件 [dvc get](https://dvc.org/doc/command-reference/get#get)

							    ```bash
								  $ dvc get https://github.com/iterative/dataset-registrytutorials/versioning/data.zip
								    
									  $ unzip -q data.zip
									    $ rm -f data.zip
										  ```

										    +  運行以下指令檢查各類別訓練資料是否各 500 筆
											    + `ls data/train/dogs | wc -l`
												    + `ls data/train/cats | wc -l`
													  + data 檔案目錄結構

													    ```bash
														  data
														    ├── train
															  │   ├── dogs
															    │   │   ├── dog.1.jpg
																  │   │   ├── ...
																    │   │   └── dog.500.jpg
																	  │   └── cats
																	    │       ├── cat.1.jpg
																		  │       ├── ...
																		    │       └── cat.500.jpg
																			  └── validation
																			       ├── dogs
																				        │   ├── dog.1001.jpg
																						     │   ├── ...
																							      │   └── dog.1400.jpg
																								       └── cats
																									            ├── cat.1001.jpg
																												         ├── ...
																														          └── cat.1400.jpg
																																    ```

																																	+ 使用 `dvc add` 追蹤現行的資料狀態

																																	  + [dvc add](https://dvc.org/doc/command-reference/add#add) 類似於 git add，會產出 `.dvc` 檔案 (類似緩存指針紀錄) 用來追蹤資料的元資料，並加入該資料集或模型進`.gitignore` 避免其被 Git 追蹤，Git 僅追蹤元資料檔案 `.dvc`

																																	    ```bash
																																		  $ dvc add data
																																		    ```

																																			+ 訓練模型

																																			  + 紀錄模型產出，或是可以使用進階的方法 [dvc run](https://dvc.org/doc/command-reference/run#run)

																																			    > We manually added the model output here, which isn't ideal. The preferred way of capturing command outputs is with [`dvc run`](https://dvc.org/doc/command-reference/run). More on this later.

																																				  ```bash
																																				    $ python train.py
																																					  $ dvc add model.h5
																																					    ```

																																						+ 交付修改內容，並加上標籤

																																						  ```bash
																																						    $ git add data.dvc model.h5.dvc metrics.csv .gitignore
																																							  $ git commit -m "First model, trained with 1000 images"
																																							    $ git tag -a "v1.0" -m "model v1.0, 1000 images"
																																								  ```

																																								  + 載入新的資料集訓練新模型

																																								    + 擴充訓練資料

																																									  ```bash
																																									    $ dvc get https://github.com/iterative/dataset-registry \
																																										            tutorials/versioning/new-labels.zip
																																													  $ unzip -q new-labels.zip
																																													    $ rm -f new-labels.zip
																																														  ```

																																														    + 檢查資料筆數是否變更
																																															    + `ls data/train/dogs | wc -l`
																																																    + `ls data/train/cats | wc -l`
																																																	  + 資料目錄結構

																																																	    ```bash
																																																		  data
																																																		    ├── train
																																																			  │   ├── dogs
																																																			    │   │   ├── dog.1.jpg
																																																				  │   │   ├── ...
																																																				    │   │   └── dog.1000.jpg
																																																					  │   └── cats
																																																					    │       ├── cat.1.jpg
																																																						  │       ├── ...
																																																						    │       └── cat.1000.jpg
																																																							  └── validation
																																																							       ├── dogs
																																																								        │   ├── dog.1001.jpg
																																																										     │   ├── ...
																																																											      │   └── dog.1400.jpg
																																																												       └── cats
																																																													            ├── cat.1001.jpg
																																																																         ├── ...
																																																																		          └── cat.1400.jpg
																																																																				    ```

																																																																					+ 追蹤新資料集與新模型

																																																																					  ```bash
																																																																					    $ dvc add data
																																																																						  $ python train.py
																																																																						    $ dvc add model.h5
																																																																							  ```

																																																																							  + 交付修改內容，並加上標籤

																																																																							    ```bash
																																																																								  $ git add data.dvc model.h5.dvc metrics.csv
																																																																								    $ git commit -m "Second model, trained with 2000 images"
																																																																									  $ git tag -a "v2.0" -m "model v2.0, 2000 images"
																																																																									    ```

																																																																										+ 切換不同工作區

																																																																										  +  [dvc checkout](https://dvc.org/doc/command-reference/checkout) 類似 git checkout 可以切換資料工作區，可以整個切換或是切換目標檔案

																																																																										    ```bash
																																																																											  $ git checkout v1.0
																																																																											    $ dvc checkout
																																																																												  ```

																																																																												    + 檢查資料
																																																																													    + `ls data/train/dogs | wc -l`
																																																																														    + `ls data/train/cats | wc -l`
																																																																															  + 如果想要只切換資料集但保留現有的分枝程式碼
																																																																															      + 運行 git status，可以看到當前分枝 `data.dvc` 被修改，元資料指向 `v1.0` 版本資料工作區，而程式碼則是指向 `v2.0` 

																																																																																    ```bash
																																																																																	  $ git checkout v1.0 data.dvc
																																																																																	    $ dvc checkout data.dvc
																																																																																		  ```

																																																																																		  ### 3. 其他 DVC 進階應用

																																																																																		  + **dvc run**

																																																																																		    + dvc add 可以追蹤資料與產出的模型檔案，若腳本程式需輸入與輸出檔案，也可以透過 [`dvc run`](https://dvc.org/doc/command-reference/run)

																																																																																			    > 如果您嘗試了在工作區版本之間切換部分中的命令，請返回主分支代碼和數據，並使用以下命令刪除 model.h5.dvc 文件：

																																																																																				    ```bash
																																																																																					    $ git checkout master
																																																																																						    $ dvc checkout
																																																																																							    $ dvc remove model.h5.dvc
																																																																																								    ```

																																																																																									    ```bash
																																																																																										    $ dvc run -n train -d train.py -d data \
																																																																																											              -o model.h5 -o bottleneck_features_train.npy \
																																																																																														                -o bottleneck_features_validation.npy -M metrics.csv \
																																																																																																		              python train.py
																																																																																																					      ```

																																																																																																						      + `dvc run` 建立一個名為 train 的 pipeline stage 於 `dvc.yaml` 檔案

																																																																																																							        ```bash
																																																																																																									      $ cat dvc.yaml
																																																																																																										        stages:
																																																																																																												        train:
																																																																																																														          cmd: python train.py
																																																																																																																            deps:
																																																																																																																			          - data
																																																																																																																					            - train.py
																																																																																																																								          outs:
																																																																																																																										            - bottleneck_features_train.npy
																																																																																																																													          - bottleneck_features_validation.npy
																																																																																																																															            - model.h5
																																																																																																																																		          metrics:
																																																																																																																																				            - metrics.csv:
																																																																																																																																							              cache: false
																																																																																																																																										        ```

																																																																																																																																												    + 與 `dvc add` 相同的方式跟踪所有輸出 (-o)。與 `dvc add` 不同，`dvc run` 還跟踪依賴項 (-d) 和運行以產生結果的命令 (python train.py)

																																																																																																																																													      > 此時可以運行 git add . 和 git commit 將 train 階段及其輸出保存到存儲庫。

																																																																																																																																														      + 使用 [`dvc repro`](https://dvc.org/doc/command-reference/repro) 指令重跑 train pipeline 若該 pipeline 依賴檔案(-d) 有修改，例如我們新增幾筆資料集的資料，並且想透過原有的 train pipeline 重新訓練模型，可以透過 `dvc repro`，指令還更新輸出並將它們放入緩存中。

																																																																																																																																															        > dvc add 和 dvc checkout 為模型和大型數據集版本控制提供了基本機制。 dvc run 和 dvc repro 為機器學習模型提供了構建系統，類似於軟件構建自動化中的 Make。

																																																																																																																																																	## 三、參考資源

																																																																																																																																																	+ [Tutorial: Data and Model Versioning](https://dvc.org/doc/use-cases/versioning-data-and-models/tutorial#tutorial-data-and-model-versioning)
