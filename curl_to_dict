import argparse
import json
from six.moves import http_cookies as Cookie
import shlex


def get_params(url):
    d = {}
    try:
        params = url.split("?")[1]
        params = params.split("&")
        for i in params:
            params_i = i
            params_i = params_i.split('=')
            pairs = zip(params_i[0::2], params_i[1::2])
            answer = dict((k, v) for k, v in pairs)
            d.update(answer)
        return d
    except:
        return {}


def parse(curl):
    try:
        parser = argparse.ArgumentParser()
        parser.add_argument('command')
        parser.add_argument('url')
        parser.add_argument('-d', '--data')
        parser.add_argument('-b', '--data-binary', default=None)
        parser.add_argument('-H', '--header', action='append', default=[])
        parser.add_argument('--compressed', action='store_true')
        parser.add_argument("-X", "--method", type=str, help="message")

        tokens = shlex.split(curl)
        parsed_args = parser.parse_args(tokens)
        site_url = parsed_args.url
        site = site_url.split("?")
        site = site[0]
        post_data = parsed_args.data or parsed_args.data_binary
        method = parsed_args.method

        if method is not None:
            method = method
        elif post_data:
            method = 'post'
        else:
            method = "get"

        if post_data:
            try:
                post_data_json = json.loads(post_data)
            except ValueError:
                post_data_json = {}
        else:
            post_data_json = {}

        cookie_dict = {}
        quoted_headers = {}
        for curl_header in parsed_args.header:
            header_key, header_value = curl_header.split(":", 1)
            if header_key.lower() == 'cookie':
                cookie = Cookie.SimpleCookie(header_value)
                for key in cookie:
                    cookie_dict[key] = cookie[key].value
            else:
                quoted_headers[header_key] = header_value.strip()

        params = get_params(site_url)
        print('URL', site)
        print('METHOD', method)
        print('HEADERS', quoted_headers)
        print('COOKIES', cookie_dict)
        print('PARAMS', params)
        print('POST_DATA', post_data_json)

        curl_dict = {
            'is_success': True,
            'url': site,
            'method': method.upper(),
            'headers': quoted_headers,
            'cookies': cookie_dict,
            'params': params,
            'data': post_data
        }
        return curl_dict
    except Exception as e:
        print('Parsing error. Details: {}'.format(e))
        return {'is_success': False}
