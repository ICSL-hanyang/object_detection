# YOLO run

./darknet detect cfg/yolov3.cfg yolov3.weights



# YOLO training

./darknet detector train data/training/person.data cfg/yolov3.cfg cfg/darknet19_448.conv.23 -gpus 0
