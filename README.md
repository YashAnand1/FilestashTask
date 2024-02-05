<div align="center">
	
# Filestash Task Collaboration 
FEB4

</div>

## Downloaded Filestash SourceCode
- Source: https://github.com/mickael-kerjean/filestash/tree/master
- Tried running the server for testing purposes but couldn't as /.src/ directory is missing from the GitHub repostory.
- Ran `npm install --force` and `npm run build` for `webpack.config.js`. Got the following error for the latter:
```
ERROR in main
Module not found: Error: Can't resolve './src' in '/home/user/Downloads/filestash-master'
resolve './src' in '/home/user/Downloads/filestash-master'
  using description file: /home/user/Downloads/filestash-master/package.json (relative path: .)
    Field 'browser' doesn't contain a valid alias configuration
    using description file: /home/user/Downloads/filestash-master/package.json (relative path: ./src)
      no extension
        Field 'browser' doesn't contain a valid alias configuration
        /home/user/Downloads/filestash-master/src doesn't exist
      .js
        Field 'browser' doesn't contain a valid alias configuration
        /home/user/Downloads/filestash-master/src.js doesn't exist
      .json
        Field 'browser' doesn't contain a valid alias configuration
        /home/user/Downloads/filestash-master/src.json doesn't exist
      .wasm
        Field 'browser' doesn't contain a valid alias configuration
        /home/user/Downloads/filestash-master/src.wasm doesn't exist
      as directory
        /home/user/Downloads/filestash-master/src doesn't exist

webpack 5.90.1 compiled with 1 error and 1 warning in 362 ms
``` 
- Ran `find | grep "/src"` and found that this directory was present in /filestash-master/vendor/github.com

## Setting Up Filestash
- mkdir filestash && cd filestash
- curl -O https://downloads.filestash.app/latest/docker-compose.yml
- docker-compose up -d
- Opened browser at http://localhost:8334 & added password
- Added S3 in STORAGE BACKEND from http://localhost:8334/admin/backend 
- Skipped Auth Middleware 

## Setting Up Minio
- Creating a container: `sudo podman run    -p 9000:9000    -p 9001:9001    -v ~/minio/data:/data    -e "MINIO_ROOT_USER=MINIO_ROOT_USER"    -e "MINIO_ROOT_PASSWORD=MINIO_ROOT_PASSWORD"    quay.io/minio/minio server /data --console-address ":9001"`
- Verifying its creation: `podman ps` which gave `8c17288dcdd4  quay.io/minio/minio:latest  9 seconds ago`
- Opening GUI: Visited `http://127.0.0.1:9000` in browser and entered `MINIO_ROOT_USER` & `MINIO_ROOT_PASSWORD` as credentials
- Created buckets in Minio and generated access & secret keys

## Using Filestash To View Buckets
- Browse http://localhost:8334/login but couldn't as I got told 'Invalid Account' due
- Visited the following to understand the issue but could not find a reason for this error:
        - https://forum.infinityfree.com/t/how-to-login-on-filestash/78274/4
        - https://www.reddit.com/r/DataHoarder/comments/gswmzf/filestash/
        - https://www.reddit.com/r/selfhosted/comments/14f740e/filestash_using_middleware_passthrough/
        - https://github.com/mickael-kerjean/filestash/issues/623
        - https://github.com/mickael-kerjean/filestash/issues/602

## Reinstalling Filestash & Minio
- Created the Minio container without `sudo podman run` again:
        - mkdir -p ~/minio/data
        - podman run -d    -p 9000:9000    -p 9001:9001    -v ~/minio/data:/data    -e "MINIO_ROOT_USER=MINIO_ROOT_USER"    -e "MINIO_ROOT_PASSWORD=MINIO_ROOT_PASSWORD"    quay.io/minio/minio server /data --console-address ":9001"
        - In the browser, created a bucket, access key and secret key
- Created the filestash container again but by referencing Aman Deep Singh Sir's respose in the '[Error] S3cmd_Connection_Error_MinIO' mail trail:
        - docker pull machines/filestash
        - docker run -itd  -p 8334:8334 -v $(pwd)/filestash:/app/data/state/ --name filestash docker.io/machines/

## Reopening Filestash
- visited http://localhost:8334/admin/setup on browser and got to see "Uncaught AjaxError: Filestash needs to be able to create/edit its own configuration which it can't at the moment. Change the permission for filestash to create and edit `/app/data/state/config/config.json`" 
        - Decided to visit the mentioned filepath using `docker exec -it filestash bash`
        - Tried to display the config.json using `cat /app/data/state/config/config.json` - Output was blank
        - Entered the config directory to see the issue using `cd /app/data/state/` and found that `config` directory was not there
        - Decided to rerun the previously Filestash container and copied its `/app/data/state/config/config.json` to my Desktop
        - I then moved this `config.json` into a `config` directory. The content of this file was:
```
        {
            "general": {
                "name": null,
                "port": null,
                "host": null,
                "secret_key": "MpUCgHZgcW7n8qdD",
                "force_ssl": null,
                "editor": null,
                "fork_button": null,
                "logout": null,
                "display_hidden": null,
                "refresh_after_upload": null,
                "upload_button": null,
                "upload_pool_size": null,
                "filepage_default_view": null,
                "filepage_default_sort": null,
                "cookie_timeout": null,
                "custom_css": null
            },
            "features": {
                "api": {
                    "enable": null,
                    "api_key": null
                },
                "share": {
                    "enable": null,
                    "default_access": null,
                    "redirect": null
                },
                "protection": {
                    "iframe": null,
                    "enable_chromecast": null,
                    "zip_timeout": null,
                    "enable": null,
                    "disable_svg": null
                },
                "office": {
                    "enable": null,
                    "onlyoffice_server": null,
                    "can_download": null
                },
                "server": {
                    "console_enable": null
                },
                "search": {
                    "explore_timeout": null
                },
                "video": {
                    "blacklist_format": null,
                    "enable_transcoder": null
                }
            },
            "log": {
                "enable": null,
                "level": null,
                "telemetry": true
            },
            "email": {
                "server": null,
                "port": null,
                "username": null,
                "password": null,
                "from": null
            },
            "auth": {
                "admin": "$2a$10$6C8orTAbzc1pFwtqpzJmkO5tl.BQSqp/PZ/chjc9ZFw1YfhYbsxmG"
            },
            "middleware": {
                "identity_provider": {
                    "type": null
                },
                "attribute_mapping": null
            },
            "constant": {
                "user": "filestash",
                "emacs": true,
                "pdftotext": true
            },
            "connections": [
                {
                    "type": "s3",
                    "label": "S3",
                }
            ]
        }
```
- Then I moved them to the **new** filestash container using `docker cp ~/Desktop/config/ filestash:/app/data/state/`

- Opened `http://localhost:8334/admin/backend` on browser, added `123` as password and performed the following actions:
        - Selected only S3 as 'Service Storage'
        - Select 'Admin' under Authentication Middleware with the following configurations:
                - Related Backend:S3
                - Access Key ID: n7Uql8oQmtU2IVhe6wwL (From Minio)
                - Secret Access Key: ARPPuvJlWwvV1D6dgOnm2UQNu1ODX7X0OlJxeRiq (From Minio)
                - Endpoint: http://192.169.122.1:9000 (For connection with Minio Container)
- Afterwards, I attempted login to FileStash on `http://localhost:8334/login` which got redirected to `http://localhost:8334/api/session/auth/?action=redirect&label=S3`. I entered the same password as admin but I kept being redirected to same page. 
        - Have not been able to login to filestash since then

------------------------------------------
# Filtering Buckets To Be Retrieved
- Refered to [this](https://min.io/docs/minio/linux/developers/go/minio-go.html) Minio resource on for connecting an application with Minio and modified [this] example for listing AWS buckets
- Instead of connecting with AWS buckets, I utilised the previous resource and modified this code to connect with Minio instead. When I ran the code, I faced the `2024/02/04 21:23:13 Error listing buckets: Access Denied.\n exit status 1` error
- I opened `localhost:9000` on my browser, added the user to admin access user policy and generated its own AccessID & Secret key. I added these credentials along with the endpoint to sample application code and was able to get all buckets like Filestash:
```
// This code is a work in progress - Contains manual filtering based on accessID & secret-key rather than filtering based on policies.

package main

import (
	"context"
	"fmt"
	"log"

	"github.com/minio/minio-go/v7"
	"github.com/minio/minio-go/v7/pkg/credentials"
)

var secretAccessKey string
var accessKeyID string

func main() {

	user := "yash"
	endpoint := "127.0.0.1:9000" //my endpoint of where i am running the minio server

	if user == "yash" {
		accessKeyID = "5CFKL8PrWtDZwBcEz204"                         //yash's user's accesskey
		secretAccessKey = "G2W1stlAatTrLgTTGIxTB7Aq9UeE4s9i7m6qdm2M" // yash's usser's secret key
	} else if user == "himanshu" {
		accessKeyID = "XZrdoHhWSjRtoXw8NnKG"                         //Himanshu's user's accesskey
		secretAccessKey = "5XUtNc9bbheiy5uWeyR1dPhZzjxsGWRIlmxEJFUr" // Himanshu's usser's secret key
	}

	// useSSL := false //i did not set this up so i could just leave this line blank and the default would be false as well
	// fmt.Println("Starting initialization\n") // for displaying where i am in the code, for my own use // can be removed

	////// connecting minio /////////////////////////////////////////////////////
	minioClient, err := minio.New(endpoint, &minio.Options{
		Creds: credentials.NewStaticV4(accessKeyID, secretAccessKey, ""), /// this is the accessid and secret key of each user!!!
		// Secure: useSSL,  /// commented this out because ssl is not set up
	})

	if err != nil {
		log.Fatalln(err) // for loggin purposes/displaying any error in case connection gets failed
	}

	// log.Printf("%#v\n", minioClient) // success message for my own reference // can be removed

	// log.Printf("Printing username and possibly its policy as well") // success message for my own reference // can be removed
	// fmt.Println(minio.NewPostPolicy())
	// fmt.Println(minioClient.EndpointURL().User)

	////// Listing specific buckets depending on specific keys //////////////////////////////////////
	buckets, err := minioClient.ListBuckets(context.Background())
	if err != nil {
		log.Fatalln("Error listing buckets:", err)
	}
	fmt.Println("The assigned buckets to the user", user, "include:\n") //this prints bucket info wih its name and meta

	for _, bucket := range buckets { /// this displays all of the keys exactly like filestash does, this is assigned to admin!
		log.Println(bucket) //this prints bucket info wih its name and meta
	}
}
```   
- In the above code I have manually defined what user has which AccessID and Secret Key, **which will actually be retrieved from frontend**. But for now, this data has been hardcoded. **NOTE: You will need to add these keys for your own users.** 
- Which user will get which buckets, has been defined in the policies in Minio. I created 2 buckets and 2 users. Yash is basically an admin user who can retrieve all buckets and Himanshu user can read only one bucket called "bucketonlyforme". 
- The policy that I have written for the Himanshu user is as follows:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketforonlyme"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucketMultipartUploads",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:s3:::bucketforonlyme/*"
            ]
        }
    ]
}
```
- In the code, if you change the username in `user == "<username>"` to Yash or Himanshu, the buckets being shown will be different. However, this approach can be **better utilised by defining policies instead of users** by retrieving them from JSON key of Minio.
- When I have defined user=="Yash", I get the following output for running the `go run listing.go ` command:
```
The assigned buckets to the user yash are:

2024/02/05 01:00:15 {bucket03 2024-02-04 16:57:11.129 +0000 UTC}
2024/02/05 01:00:15 {bucketforonlyme 2024-02-04 16:56:18.776 +0000 UTC}
```
- When I defined user=="Himanshu", I get the following output:
```
The assigned buckets to the user himanshu are:

2024/02/05 01:00:15 {bucketforonlyme 2024-02-04 16:56:18.776 +0000 UTC}
```

## EOD Conclusion
Mr. Himanshu and I were able to retrieve specific buckets based on the usernames and now the work plan includes modifying the existing code to retrieve JSON Key from Minio and provide buckets based on that.  

_________________________
_________________________
_________________________

<div align="center">
	
# Filestash Task Collaboration 
FEB4

</div>

<div align="center">

# February 5 Documentation
</div>

# Sample Filestash App Modification
- From [this GetBucketPolicy section from MINIO Documentation](https://min.io/docs/minio/linux/developers/go/API.html#getbucketpolicy-ctx-context-context-bucketname-string-policy-string-error), I implemented the following to the sample application:
```
policy, err := minioClient.GetBucketPolicy(context.Background(), "bucket03")
if err != nil { c
    log.Fatalln(err)
}
```
- In the above code, the "bucket03" is the policy that I was to be retrieving buckets for. However, this is not retrieving policies based on user but rather based on buckets.
- The question is: How do we retrieve policies based on specific users.
_______________

# Running Server From /filestash/cmd/main.go
## Issue 1
- I faced the following error for running `go run main.go`:
```
go run main.go
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
In file included from ../server/plugin/plg_image_c/image_raw.go:3:
./image_raw.h:3:10: fatal error: libraw/libraw.h: No such file or directory
    3 | #include <libraw/libraw.h>
      |          ^~~~~~~~~~~~~~~~~
compilation terminated.
```
- In order to solve this issue, I utilised the following resources:
        - [Libraw Installation](https://ubuntu.pkgs.org/20.04/ubuntu-main-amd64/libraw-dev_0.19.5-1ubuntu1_amd64.deb.html)
        - [LibRaw Forum Post](https://www.libraw.org/node/2535)
        - I understood that this error was occuring due to missing /libraw/libraw.h package
- I utilised the [Libraw Installation](https://ubuntu.pkgs.org/20.04/ubuntu-main-amd64/libraw-dev_0.19.5-1ubuntu1_amd64.deb.html) for installing this library to `/filestash-master/server/plugin/plg_image_c`:
        - sudo apt-get install libraw-dev

## Issue 2
After this installation, when I ran `main.go` again in `/filestash-master/cmd`, I got:
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_gif.c:4:10: fatal error: gif_lib.h: No such file or directory
    4 | #include <gif_lib.h>
      |          ^~~~~~~~~~~
compilation terminated.
```
        - In order to solve this, I utilised [this Ubuntu Package resource](https://ubuntu.pkgs.org/20.04/ubuntu-main-amd64/libgif-dev_5.1.9-1_amd64.deb.html) to download libgif in the `filestash-master/server/plugin/plg_image_c` directory.

## Issue 3
Running `main.go` again in `/filestash-master/cmd` gave:
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_gif.c:5:10: fatal error: webp/encode.h: No such file or directory
    5 | #include <webp/encode.h>
      |          ^~~~~~~~~~~~~~~
compilation terminated.
```
        - Resource utilised: [Ubuntu.PKGS.org/libwebp-dev](https://ubuntu.pkgs.org/20.04/ubuntu-main-amd64/libgif-dev_5.1.9-1_amd64.deb.html) 
        - Ran `sudo apt-get install libwebp-dev` to install this library

Ran the following for installing these


1. 
1. 
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
In file included from ../server/plugin/plg_image_c/image_raw.go:3:
./image_raw.h:3:10: fatal error: libraw/libraw.h: No such file or directory
    3 | #include <libraw/libraw.h>
      |          ^~~~~~~~~~~~~~~~~
compilation terminated.
```
- sudo apt-get install libraw-dev

2.
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_gif.c:4:10: fatal error: gif_lib.h: No such file or directory
    4 | #include <gif_lib.h>
      |          ^~~~~~~~~~~
compilation terminated.
```
- sudo apt-get install libgif-dev

3. 
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_gif.c:5:10: fatal error: webp/encode.h: No such file or directory
    5 | #include <webp/encode.h>
      |          ^~~~~~~~~~~~~~~
compilation terminated.
```
- sudo apt-get install libwebp-dev

4. 
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_heif.c:3:10: fatal error: libheif/heif.h: No such file or directory
    3 | #include <libheif/heif.h>
      |          ^~~~~~~~~~~~~~~~
compilation terminated.
```
- sudo apt-get install libheif-dev
- sudo apt-get install libheif1

5. 
```
 github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_heif.c:4:10: fatal error: jpeglib.h: No such file or directory
    4 | #include <jpeglib.h>
      |          ^~~~~~~~~~~
compilation terminated.
```
- Read: [StackOverflow Post](https://stackoverflow.com/questions/42292877/fatal-error-jpeglib-h-no-such-file-or-directory) 
- sudo apt-get install libtiff-dev
- sudo apt-get install libjpeg-dev

6. 
```
# github.com/mickael-kerjean/filestash/server/plugin/plg_image_c
image_png.c:4:10: fatal error: png.h: No such file or directory
    4 | #include <png.h>
      |          ^~~~~~~
compilation terminated.
```
- sudo apt-get install libpng-dev

7. Result
```
2024/02/05 14:08:40 SYST INFO Filestash v0.5 starting
2024/02/05 14:08:40 SYST INFO [http] starting ...
2024/02/05 14:08:40 SYST ERROR error: listen tcp :8334: bind: address already in use
```

8. Stopping 8334 Service
```
**netstat -tulnp | grep 8334**
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 0.0.0.0:8334            0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::8334                 :::*                    LISTEN      -    

**ps -aux | grep 8334** # Becasue process-id not showing up



```
