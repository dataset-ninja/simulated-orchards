Dataset **Simulated-Orchards** can be downloaded in [Supervisely format](https://developer.supervisely.com/api-references/supervisely-annotation-json-format):

 [Download](https://assets.supervisely.com/remote/eyJsaW5rIjogInMzOi8vc3VwZXJ2aXNlbHktZGF0YXNldHMvMjg3OV9TaW11bGF0ZWQtT3JjaGFyZHMvc2ltdWxhdGVkLW9yY2hhcmRzLURhdGFzZXROaW5qYS50YXIiLCAic2lnIjogIjdkZ2Nid1BPMStJcVRwc2JSc0N3TUdGM1M2WGloM2lGSDc3N1dIMk03QzA9In0=?response-content-disposition=attachment%3B%20filename%3D%22simulated-orchards-DatasetNinja.tar%22)

As an alternative, it can be downloaded with *dataset-tools* package:
``` bash
pip install --upgrade dataset-tools
```

... using following python code:
``` python
import dataset_tools as dtools

dtools.download(dataset='Simulated-Orchards', dst_dir='~/dataset-ninja/')
```
Make sure not to overlook the [python code example](https://developer.supervisely.com/getting-started/python-sdk-tutorials/iterate-over-a-local-project) available on the Supervisely Developer Portal. It will give you a clear idea of how to effortlessly work with the downloaded dataset.

The data in original format can be [downloaded here](https://www.kaggle.com/datasets/dylanhasperhoven/simulated-orchards).