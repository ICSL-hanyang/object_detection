# 자동 라벨링 프로그램 만들기

yolo 설치 후 darknet/exmaples 폴더안에있는 코드를 수정

detector.c 파일
```c
#define DATASET_NUM 데이터개수

void test_detector(char *datacfg, char *cfgfile, char *weightfile, char *filename, float thresh, float hier_thresh, char *outfile, int fullscreen)
{
    list *options = read_data_cfg(datacfg);
    char *name_list = option_find_str(options, "names", "data/names.list");
    char **names = get_labels(name_list);

    image **alphabet = load_alphabet();
    network *net = load_network(cfgfile, weightfile, 0);
    set_batch_network(net, 1);
    srand(2222222);
    double time;
    char buff[256];
    char *input = buff;
    float nms=.45;
    int i=1;
    while(i<=DATASET_NUM){
        // if(filename){
        //     strncpy(input, filename, 256);
        // } else {
        //     printf("Enter Image Path: ");
        //     fflush(stdout);
        //     input = fgets(input, 256, stdin);
        //     if(!input) return;
        //     strtok(input, "\n");
        // }
        char buf_path[100]="/home/kdh/ml/darknet/data/dog"; //데이터셋 절대경로
        char buf_jpg[5]=".jpg"; //데이터셋 확장자
        char buf_imagenum[100];
        char buf_filename[256];
        char *filenames = buf_filename;
        sprintf(buf_imagenum,"%d",i++);
        filenames = buf_path;
        filenames = strcat(filenames,buf_imagenum);
        // printf("1 %s\n",filenames);
        filenames = strcat(filenames,buf_jpg);
        // printf("2 %s\n",filenames);

        // for(int j=0;j<256;j++){
        //     printf("%c",filenames[j]);
        // }
        // filenames = strcat("/home/kdh/ml/darknet/data/dog",filenames_num);
        // printf("%s",filenames);
        // filenames = strcat("filenames",".jpg");
        // printf("%s",filenames);
        strncpy(input, filenames,256);
        image im = load_image_color(input,0,0);
        image sized = letterbox_image(im, net->w, net->h);
        //image sized = resize_image(im, net->w, net->h);
        //image sized2 = resize_max(im, net->w);
        //image sized = crop_image(sized2, -((net->w - sized2.w)/2), -((net->h - sized2.h)/2), net->w, net->h);
        //resize_network(net, sized.w, sized.h);
        layer l = net->layers[net->n-1];


        float *X = sized.data;
        time=what_time_is_it_now();
        network_predict(net, X);
        printf("%s: Predicted in %f seconds.\n", input, what_time_is_it_now()-time);
        int nboxes = 0;
        detection *dets = get_network_boxes(net, im.w, im.h, thresh, hier_thresh, 0, 1, &nboxes);
        //printf("%d\n", nboxes);
        //if (nms) do_nms_obj(boxes, probs, l.w*l.h*l.n, l.classes, nms);
        if (nms) do_nms_sort(dets, nboxes, l.classes, nms);
        draw_detections(im, dets, nboxes, thresh, names, alphabet, l.classes);
        free_detections(dets, nboxes);

        fflush(stdout);
        filename=NULL;
        //         if(outfile){
        //             save_image(im, outfile);
        //         }
        //         else{
        //             save_image(im, "predictions");
        // #ifdef OPENCV
        //             make_window("predictions", 512, 512, 0);
        //             show_image(im, "predictions", 0);
        // #endif
        //         }

        // free_image(im);
        // free_image(sized);
        // if (filename) break;
    }
}
```

----------------------------------------------------------------------------

draw_detections 함수에서 object의 정보가 나오므로 이 함수를 수정하여준다.

image.c 파일
